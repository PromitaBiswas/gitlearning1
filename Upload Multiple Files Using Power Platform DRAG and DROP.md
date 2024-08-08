# Upload Multiple Files Using Power Platform DRAG and DROP

## Introduction

It can be a common requirement to have a file attachment to be uploaded to backend repositories like SharePoint or Blob Storage using Power Apps form.But if the requirement is to upload multiple files at a time and by drag and drop, below steps will be the solution for you.

By using below solution, we are uploading multiple files of various types into SharePoint Document Library.

## Implementation

### Steps

1.Open [Power Apps portal](https://make.powerapps.com/) and create a blank canvas app with appropriate name.
<img src="images/bulk upload PA/Picture1.png" alt="Pic1" width="600">

2.As Power Apps does not have Attachment control directly, we will add a data source which has an attachment field and create a Form.
<img src="images/bulk upload PA/Picture2.png" alt="Pic2" width="600">

3.Then bring the Attachment control out of the Form and rename. At this stage the Form and data source can be deleted.

<img src="images/bulk upload PA/Picture3.png" alt="Pic3" width="600">

<img src="images/bulk upload PA/Picture4.png" alt="Pic4" width="600">

4.To remove the errors, remove the values from Tooltips and Items property. Change the BorderColor to Color.Black and DisplayMode to DisplayMode.Edit.

5.Now create a Power Automate Flow.
<img src="images/bulk upload PA/Picture5.png" alt="Pic12" width="300">


6.Select file as a user input.
<img src="images/bulk upload PA/Picture6.png" alt="Pic6" width="600">

7.Add an action - create SharePoint file.
<img src="images/bulk upload PA/Picture7.png" alt="Pic7" width="600">

8.Add values to the parameters as below and save the flow with proper name.
<img src="images/bulk upload PA/Picture8.png" alt="Pic8" width="600">
<img src="images/bulk upload PA/Picture9.png" alt="Pic9" width="600">

9.Now coming back to Power Apps, add a button and write below code in the “OnSelect” property.
ForAll(AttachmentControl.Attachments As FILE, Uploadfiles.Run({file:{contentBytes:FILE.Value,name:FILE.Name}}))
<img src="images/bulk upload PA/Picture10.png" alt="Pic10" width="600">

10.Save and run the App, drag and drop multiple files and it will be uploaded in the SharePoint location specified in Power Automate.

**Note:** Default limit of max number of files which can be attached is 6 and file size is 10 MB. It can be changed by changing the below properties. There is no limitation on file type and any file can be uploaded by this solution.

<img src="images/bulk upload PA/Picture11.png" alt="Pic11" width="600">
