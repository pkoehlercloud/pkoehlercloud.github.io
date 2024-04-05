---
title: "Windows 365 - Full Image Guide"
image: "title.png"
date: 2022-12-25T16:56:47+06:00
draft: false
author: "Patrick Koehler"
tags: ["Windows 365", "Intune", "Endpoint Management"]
categories: ["Windows 365"]
---

Hello everyone and welcome back to the Windows 365 full guide. This week we continue the guide with Image Management capabilities and what you need to take care of, when replacing the currently used image for your Cloud PCs.

Mainly I will focus on these topics:
* Image prerequisites for Windows 365
* Image preperation
* Image replacement strategies
* Important notice

## Image prerequisites for Windows 365
Windows 365 offers the possibility to import individual images for your Cloud PCs. But why does this makes sense, as we can use Microsoft Endpoint Manager to install apps and apply configurations on our persistent Desktops?
Simply for the reason of generalization and streamlining the desktop experience for all users! Microsoft Endpoint Manager (covered in detail in the following chapter) should be used from my perspective for the overall management, but to deploying a master image with most prerequisites applied already should be mandatory for most organizations. 
To deploy images for Windows 365 there are some important prerequisites that you need to keep in mind prior to plan the image, as your Azure Virtual Desktop images (reyling on Windows 10 Enterprise Multi-Session) CANNOT be reused. Before you wrap up a new image on Microsoft Azure, ensure that your virtual machine fullfills the following requirements
 
* OS must be Windows 10 Enterprise (No Multi-Session)
* Images must be generalized prior to be used for WIndows 365
* Virtual Machine generation must be GEN1

## Image preperation
Now that we know the prerequisites we can start creating a new virtual machine on Azure. In this article, I will just show you how to prepare the virtual machine and how to generalize it. Customizations are depending on your organization and apps that you might want to deploy to your users and can’t be fully considered. However, I’ve written a guide with best practices, which can be downloaded here: 


## Create new image virtual machine on Azure
As a first step, before installing all the required apps and set the configurations needed, we need to create a virtual machine from Azure as a starting point. 
First, we need to login to the Azure Portal and navigate Create a resource, alternatively go to Virtual Machines and click create.
 
Once you’ve clicked Create a resource you’re in the Azure Marketplace. Search for Windows 10 as a term and select Microsoft Windows 10 once available. 
 
Now you can select the version that you want to use for your Golden Image. In my case I choose to use Windows 10 Enterprise, Version 21H1. Make sure that you don’t select Windows 10 Enterprise Multi-Session versions as they cannot be used with Windows 365. 
 
Select on Create to continue. 
Now you just need to fill out the required information, such as Resource Group, VM name, the region that you want to deploy as well as the size. For an image I use the recommended size or a Standard_D4ds_v4 or comparable. Set the local Admin password and the network preferences as you wish. 
 
The most important thing, after providing the information is to switch to the Advanced tab in your virtual machine creation process and scroll down the page. 
 
Before you continue, make sure Gen 1 is selected, otherwise your image wont be imported!
Once you ensured the configuration is correctly set, navigate to the Review + create page and create the virtual machine. 

Perform customizations + Virtual Machine generalization
Now that we’ve successfully deployed the virtual machine, you can continue to install your apps and configurations that need to be done. 

Once this task is complete you need to generalize the virtual machine to be used for Windows 365. To do this, you must open the Windows Explorer and navigate to C:\Windows\System32\Sysprep
 
Open the sysprep.exe, check the generalize box and select the Shutdown option (very important). 
 
Once this is done and you initiate the generalization process. If you might encounter issues, make sure that you validate the installed AppX apps on the system as they can produce issues in the image creation process. 
 
Capture the virtual machine to become an Image for Windows 365
As a next step, we need to switch back to the Azure portal, where we’ll find the virtual machine in a stopped state. Select the virtual machine, and click on Stop in the top bar to also deallocate the machine. 
 
The status of the VM will change to Stopped (Deallocated). 
 
The next step is to click on Capture in the top bar, which brings us to the following menu: 
 
Select the Resource Group, where you want to store our new image, define if you want to store the image inside of a Shared Image Gallery (recommended for production workloads and multiple images to maintain). In my case I’m doing this for a single image, so I select No, capture only a managed image and I provide a name, before continuing to Tags and to Review + create, where I finally create the image. 
 
Keep in mind that the virtual machine associated to that image is unusable after the creation! 
 
Importing the image to Windows 365
Finally, we’ve created our new image that we can now important to Windows 365 to be provisioned for our Cloud PCs. Please log into the Microsoft Endpoint Manager admin center using https://endpoint.microsoft.com. Select Devices and choose Windows 365 under Provisioning.  
 
In the previous chapters we’ve seen how easy it is to create a new provisioning policy, now we will have a closer look at how to import the image, so we select Device images. The name can be quite confusing as the images will be used for virtual machines. Arrvied in the Device image tab, we click + Add to import our recently created Azure image. You might have seen some articles stating that you must import the image from a Hyper-V environment, which is not true, however it can be an option if you’re forced to prepare the image on-premises. 
 
Once we’ve selected + Add, we can provide again some information like the Image name, the image version (e.g. 1.0.0) and pick the source image from the drop down list. 
Important, the image must be in the same Subscription associated to the On-Premises network connection we have created earlier.  
 
Click on Upload, once you’ve finished the editing process. 
The overall process took 60 minutes to complete, afterwards the image will be presented as follows:
  
## Image replacement strategies
After successfully creating and importing the image, I want to show the options to replace images and there are quite a few things you must consider before applying any new provisioning policy to your users or before you replace any images:

* Can you ensure that your users have their data backed up on OneDrive / SharePoint etc.?
* Can you ensure that no app critical data can get lost? (e.g. License files in the local user profile)
* Did you communicate the changes with your end users?
* Did you choose a good time to re-provision your desktops as this process can take up to 40 minutes?

In general, it makes sense to import the image before deploying production Provisioning policies, however in case that you must re-provision desktops I personally went for the following strategy.

First I switched to the Provisioning policies tab in the Windows 365 menu. 

Instead of creating a new policy, I select the one that I’ve already created (because I want to provision the updated image to my end users). 
 
In the center screen we can click on edit to select the image / image version that we want to pick.
 
Select Custom Image as Image type from the drop down menu.
 
Now you can select our recently created image / version from the right hand side menu.
 
Click on Select and continue the process when clicking on Next. Then click Update on the final page to complete the update of the provisioning policy. 
 
But what is the result from updating the policy? Actually nothing in the beginning, as the Cloud PCs require to be reprovisioned from Endpoint Manager. At the moment this is not done in bulk, but maybe there will be soon a possibility to. 

If you want to continue, go back to the Microsoft Endpoint Manager admin center and select Devices > Windows and select one of your provisioned Cloud PCs that you want to equip with the new image. 
Once this is done, you just need to select Reprovision from the top bar of the selected Cloud PC. Click Yes to complete the task. 
 
 
## Important notice!
ATTENTION, before you reprovision as it might have a huge impact on your user’s configuration and files as it will be reset to default values unless you have them backed up somewhere. I’m really looking forward to the FSLogix integration, as this will remediate exactly this issue.
