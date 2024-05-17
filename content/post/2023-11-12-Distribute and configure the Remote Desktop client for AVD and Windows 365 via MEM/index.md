---
title: "Distribute and Configure the Remote Desktop app via Intune"
image: "title.png"
date: 2023-11-12T18:12:14+06:00
draft: true
author: "Patrick Koehler"
tags: ["Windows 365", "Azure Virtual Desktop", "Microsoft Intune", "Endpoint Manager"]
categories: ["Azure Virtual Desktop", "Intune"]
description: "Discover how to distribute the Remote Desktop Client for Azure Virtual Desktop and the upcoming Windows 365 Cloud PC service through Microsoft Endpoint Manager (Intune) in this blog. Learn the step-by-step process, from packaging the Remote Desktop Client to uploading it to Intune, importing it to your tenant, configuring auto feed discovery, and more. Enhance user experience and streamline access to remote computing resources from the Microsoft Cloud. "
---

Hi everyone,

today I have another blog for you about how to distribute the Remote Desktop Client for Azure Virtual Desktop and the upcoming service release of Windows 365 Cloud PC. We will perform the distribution to your clients via Microsoft Endpoint Manager (Intune).

The first thing we need to do is to package our Remote Desktop Client in the current version: 1.2.2130

Please find the latest version here: [What’s new in the Windows Desktop client | Microsoft Doc](https://web.archive.org/web/20230321230532/https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)

<b>Packaging of the Remote Desktop Client app and upload to Microsoft Endpoint Manager (Intune)</b>

The first thing we need to do now is to select the architecture, we use to distribute the client. In my case, I take the 64-bit client and download it to my computer.

I’ve used the following download link for the 64-bit version: (https://go.microsoft.com/fwlink/?linkid=2139233)

Now I copy the downloaded file to a location that is easily accessible from my packaging PC. For this reason, I create a new folder called: “RemoteDesktopClient_122130″ in my favorite location.

In the next step, we need to download the Microsoft Win32 Content Prep tool, to package our MSI file into an IntuneWin container.

Go to the following GitHub page and download the exe: (https://web.archive.org/web/20230321230532/https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool)

For this demonstration I’ve created a folder called “MEM” where I store the file.

Now, we need to open a PowerShell window with elevated rights (Administrator) and set the Execution Policy to “RemoteSigned” and approve with “Y(es)”:

```powershell
Set-ExecutionPolicy RemoteSigned
```

Next, we navigate to the folder, that has our IntuneWinAppUtil, which is in my example C:\MEM. Do this using:
Now we need to specify the Source file, which is our recently downloaded Remote Desktop Client and the destination location for the IntuneWin file. We start with opening the IntuneWinAppUtil.

```powershell
cd C:\MEM
.\IntuneWinAppUtil.exe
```

The tool asks us to specify the source folder, we select the one where our Remote Desktop Client is stored, give the tool the file name and select the output folder. We don’t want to specify a catalog folder.

We can see the intunewin file besides the MSI file.

### Import Remote Desktop Manager via MEM (Intune)

To import the package to Intune, we need to login first to our tenant using: (https://endpoint.microsoft.com)

Now we need to navigate to Apps / Windows

Now we see the collection of all Windows apps available and we click on “+Add” to import our Remote Desktop Client.

Now we need to select the App type, which is “Windows app (Win32)” and click on select.

The process asks us now for the app package file, which is the intunewin file we’ve just created.

Once everything is fine, we click on OK and specify the parameters for the installer. In my case I only had to add the Publisher information to the package. I specified “Microsoft” and click on next.

The advantage of using an MSI is that the IntuneAppWinUtil automatically detects the installation parameters, additional ones can be added, but that’s not required and we can continue with the next steps.

In the next tab, we need to provide the requirements, which is obviously the 64-bit architecture and the minimum operating system (that can be adjusted to your environment), which is Windows 10 1909 in my case. We click next, once finished.

The next step requires us to define a detection policy, we click on “+Add” and specify the MSI product code option, as this is already prefilled.

For the next steps we don’t define dependencies, supersedence and we can continue to select the Scope Tags and do the assignment if needed. In my case I select a Windows 11 based laptop that is completely Azure AD joined. I define to install the app asap with no user interaction (so Toast notifications wont appear).

Last but not least we validate the configuration and apply it.

### Configure auto feed discovery for the Remote Desktop Client app (MEM and GPO)

Now we want that our users don’t need to log into the Remote Desktop Client with their user credentials. For this reason, we can define a configuration policy in Intune, which helps us to improve the user experience.

Now that the Remote Desktop App is prepared, we need to navigate to Devices > Configuration policies in Intune and create a new profile.

Select “Windows 10 and later” as a platform and the profile type as “Settings catalog (preview)“.


We name our policy “Remote Desktop Client Auto Feed Registration” and click on next.


Now we click on “+Add settings” and type “remote desktop” in the search bar. Scroll down to “Administrative Templates / Windows Components / Remote Desktop Services / Remote App and Desktop Connections”


Check the box for “Specify default connection URL (User)“. Back in the settings, switch the toggle to “Enabled” and specify the “Default connection URL” as: https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery


IMPORTANT! Keep in mind that this setting is currently only available for Windows Insiders! For all other use cases you can use the corresponding Group Policy Setting in your on-premises environment to configure the auto configuration.


The last step is to select the users/groups you want to assign this policy to and create it!

And that’s it – a super-easy way of packaging and deploying as well as auto-configuring the Remote Desktop Client for accessing your remote computing resources from the Microsoft Cloud!

If you have any questions, feel free to reach out in the comments or on Social Media, I’m always happy to help!

Cheers,

Patrick