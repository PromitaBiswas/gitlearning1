# Extracting AAD Group Members Using Power Automate

## Introduction

In Power BI and many other applications, we control user access through Azure Active Directory security groups. It’s very crucial to know the usage metrics of those applications in most of the projects, which we can get by log analysis mechanisms.

But sometimes it is equally beneficial to report on all users who have access to the application. This helps us to know the percentage of users who are active or inactive, which can be a parameter to understand the importance of the application for business.

This article will explain how to extract Member list from AAD groups using Power Automate and save them in SharePoint Directory which then can be used for reporting purpose.

## Implementation
Steps:

1.Open [Power Automate Portal](https://make.powerautomate.com/environments/e39bdacf-c4ae-4d0d-8d8a-3dd1373f1027/home) and create a scheduled flow.
<img src="images/AAD Group/Step1.png" alt="Pic1" width="600">

2.Provide flow name ,choose the schedule occurrence and click on create.
<img src="images/AAD Group/Step2.png" alt="Pic1" width="600">

3.Once you reach to below screen click “New Step”.
<img src="images/AAD Group/Step3.png" alt="Pic1" width="600">

4.Choose an operation called “Initialize Variable” and it should be “Array” type.
<img src="images/AAD Group/Step4.png" alt="Pic1" width="400">

5.Add new step and search for Office 365 Groups and select “List my owned groups”.
<img src="images/AAD Group/Step5.1.png" alt="Pic1" width="600">
<img src="images/AAD Group/Step5.2.png" alt="Pic1" width="600">

6.In next step add an operation “Apply to each”.
<img src="images/AAD Group/Step6.png" alt="Pic1" width="600">

7.Select value from “Dynamic content”.
<img src="images/AAD Group/Step7.png" alt="Pic1" width="600">

8.Select “Add an action” and select “List group members”.
<img src="images/AAD Group/Step8.png" alt="Pic1" width="600">

9.In “Group Id” field choose “Enter custom value”. Then below steps will be applied for all the groups you own. In case only a particular group need to be chosen, it can be done from the given drop down.
<img src="images/AAD Group/Step9.png" alt="Pic1" width="600">

10.Choose “Group Id” as shown below.
<img src="images/AAD Group/Step10.png" alt="Pic1" width="600">

11.Again, add an action “Apply to each” and select value.
<img src="images/AAD Group/Step11.png" alt="Pic1" width="600">

12.Add an action inside “Apply to each 2” “Append to array variable”. Choose the variable initialized before and construct the value as below. Other values also can be added as per requirement. It will complete the step “Apply to each 2”.

<img src="images/AAD Group/Step12.png" alt="Pic1" width="600">

13.Next step will be to “Compose”, and Inputs will be the array variable.
<img src="images/AAD Group/Step13.png" alt="Pic1" width="600">

14.Last step will be to save the content in SharePoint Directory. Choose “Create file” action as shown below and provide input. This will complete the flow, save and do test run.
<img src="images/AAD Group/Step14.1.png" alt="Pic1" width="600">
<img src="images/AAD Group/Step14.2.png" alt="Pic1" width="600">

15.This flow will extract members from AAD groups you own and save them in SharePoint directory as per schedule. It will replace the previous file each time it will run. If you don’t want to replace it, add timestamp along with File name.

<img src="images/AAD Group/Step15.1.png" alt="Pic1" width="600">
<img src="images/AAD Group/Step15.2.png" alt="Pic1" width="600">

16.In SharePoint Directory a file will be created like below and it can be accessed from Power BI for further reporting. Here timestamp is not added, so each time this file will be replaced by new file.
<img src="images/AAD Group/Step16.png" alt="Pic1" width="600">
