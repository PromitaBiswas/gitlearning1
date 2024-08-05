# Reading Large Image as Binary in Power BI
## Introduction

In many scenarios we embed images in Power BI report using binary data. One of the scenarios is when we get new images on a periodic basis in a sharepoint location, and we need to pick and display the latest image in dashboard or need to have the functionality to display respective images as per the period selected in the slicer.

In these cases, we tend to store the images in a shared folder and try to read the content as a table using power query. However, in multiple case we face difficulty in loading full image. This is due to the 32,677-character limit of text column which restricts us from embedding the full image.

This article will show an intuitive way to embed images with complete original size. Example, below is an image of Shell Branding (Image 1) but when we tried to load it using its binary value we only got the upper portion not the full image (Image 2).

Image 1:

<img src="/images/Reading_binary/Pic1.png" alt="Pic1" width="600">

Image 2:

<img src="/images/Reading_binary/Pic2.png" alt="Pic2" width="600">

## Approach

We know that we can't store Base64 characters (Text representation of the image) more than 32,677 characters, so as a workaround, we need to store this information in multiple rows. Each of the cells will have less than 32,677 characters, and later, we will reassemble these cells into a single DAX measure.

The idea is to remove the text limitation using the DAX measure. We can store 2.1 million characters in DAX measure, so it’s a lot that could be used to store image information.

## Implementation

Steps:
1. Connect Power BI with SharePoint folder or where the image is saved and load it as a table.

<img src="/images/Reading_binary/Pic3.png" alt="Pic3" width="600">

2. Now we need to create a Power Query Function.

**Flow:**
- This function will accept the table as an input parameter and select only two columns from the table “Content” and
   “Name” of the images.
- Define the inline function "ConvertOneFile" that will be responsible for splitting all the binary content into
  multiple Lists with Name information.
- Loop over all the binary data in the table to call above defined function.
- Combine all the lists for a single image.
- Convert lists to the table and add an index column.

 **M Query:**

let
    Source = Folder.Files("Folder path"),
    #"Filtered Rows" = Table.SelectRows(Source, each ([Name] = "Capture.JPG")),
     //Remove unnecessary columns
    RemoveOtherColumns = Table.SelectColumns(#"Filtered Rows",{"Content", "Name"}),
    //Creates Splitter function
    SplitTextFunction = Splitter.SplitTextByRepeatedLengths(30000),
    //Converts table of files to list
    ListInput = Table.ToRows(RemoveOtherColumns),
    //Function to convert binary of photo to multiple
    //text values
    ConvertOneFile = (InputRow as list) =>
        let
            BinaryIn = InputRow{0},
            FileName = InputRow{1},
            BinaryText = Binary.ToText(BinaryIn, BinaryEncoding.Base64),
            SplitUpText = SplitTextFunction(BinaryText),
            AddFileName = List.Transform(SplitUpText, each {FileName,_})
        in
            AddFileName,
    //Loops over all photos and calls the above function
    ConvertAllFiles = List.Transform(ListInput, each ConvertOneFile(_)),
    //Combines lists together
    CombineLists = List.Combine(ConvertAllFiles),
    //Converts results to table
    ToTable = #table(type table[Name=text,Pic=text],CombineLists),
    //Adds index column to output table
    AddIndexColumn = Table.AddIndexColumn(ToTable, "Index", 0, 1, Int64.Type)
in
    AddIndexColumn
  
The output after this function execution will be like below.

<img src="/images/Reading_binary/Pic4.png" alt="Pic4" width="600">

3. Last step will be to create a DAX Measure. This will reassemble all the BASE 64 content into a single measure. We
   need to set the data category of this measure to “Image URL”.

**DAX formula:**

Display Image =
IF(
HASONEVALUE('Table_Name' [Name]),
"data:image/jpeg;base64, " &
CONCATENATEX(
'Table_Name',
'Table_Name' [Pic],
,
'Table_Name' [Index],
ASC)
)

Comparison of before (1st image) and after workaround image (2nd image) -
<img src="/images/Reading_binary/Pic5.png" alt="Pic5" width="600">

## Limitations

1. As we are storing all the data of images in Power BI dataset so that could increase the data refresh time.
2. As DAX measure can only store max 2.1 million characters; if we load very large images, it will give an error.
