---
title: "Microsoft Edge – The complete guide for AVD"
description: "meta description"
image: "title.png"
date: 2021-03-08T16:56:47+06:00
draft: false
author: "Patrick Koehler"
tags: ["Windows 365", "Azure Virtual Desktop", "Microsoft Edge", "Azure", "Windows 11", "Windows 10"]
categories: ["EUC", "Azure Virtual Desktop", "Azure","User Experience"]
---

Hi everyone,

it’s been a while since my last blog post and I think it’s getting time for a very important topic for your end users – BROWSING!

Regardless of which browser you’re using, we know that the correct configuration, paired with optimization and security is key to let users work smoothly.

But why do we need to optimize the browsing experience?

I’m sure you know at least one of the types of users:

At least 15 tabs open because closing is too dangerous and pages need to be directly accessible
From those 15 tabs, 3 are videos where specific playlists are running, even if the Tab itself is muted
The virtual session starts to slow down over time
All these mentioned topics depend on the right setup of your Browsing experience.

## Overview – Microsoft Edge – Chromium

![alt text](image.png)

Microsoft released the evolution of its classic Edge browser on 15. January 2020, so about one year ago. Since October 2020, the browser will be installed by default into the operating system by Windows Version 20H2. This version of Microsoft Edge is based on Chromium, an open-source project behind the Google Chrome Browser project.

But the Edge Chromium version isn’t just a copy of Google Chrome with a Microsoft branding – furthermore it includes additional functionalities and removes several interfaces to Google directly (which are obviously part of the Chromium project itself).

Some of the advantages, why to choose the Edge Chromium Browser are:
* Own Add-On store Microsoft Edge-Add-Ons (https://microsoftedge.microsoft.com/addons/Microsoft-Edge-Extensions-Home?hl=de)
* Advanced Privacy options, which can be configured using this link (edge://settings/privacy)
* Progressive Web Apps (literally converting Websites in to small apps which can be added to the Desktop or Startmenu – basically an advanced shortcut)

## Edge Stable version 89 released

Microsoft announced on 04.03.2021, that the new stable version 89 of Edge is being released.
The official link can be found here: (https://blogs.windows.com/msedgedev/2021/03/04/edge-89-performance/)

A summary of the key features can be found below:
* Startup boost functionality – improves app start time from 29% – 41% depending on your hardware resources
* Sleeping Tabs – Previously, Tabs that were opened in the background, still consumed hardware resources on the system. With resource heavy websites opened (such as video portals), the performance might suffer a lot! This has been tackled by Sleeping Tabs

## Impact on Azure Virtual Desktop

Especially those two new features, have a major impact on systems with shared hardware resources such as Windows 10 Enterprise Multi-Session. As browsing is an essential part of our work time and several applications transforming from classical Windows Apps to web applications, speed and intelligent resource consumptions are key to operate a virtual desktop environment.

## Chapter 1 – Configure Sleeping Tabs

To enable the sleeping tabs functionality, simply type the following path into the address bar of your Edge Chromium browser:

```powershell
edge://flags/#edge-sleeping-tabs
```

You will be presented with the option to enable the setting, by clicking on “Default” on the right side and select “Enabled” from the drop-down list.

Afterwards, Microsoft Edge requires you to perform a restart of the browser.

Now, back in the browser, you can use the following path in the address bar to adjust your settings:

```powershell
edge://settings/?search=sleeping%20tabs
```

The now visible options allow you to configure, when a tab will be set to “sleep”. By default this value is set to 2 hours of inactivity. I recommend you to change this value to really benefit of resource savings. I made good experiences, which the setting defined to 15 minutes. Simply click on the drop down of the value and select what fits for you the best.

If there are pages, you never want to go to sleep, you can select and define them below “add” underneath the point “Never put these sites to sleep”.

Type the URL or wildcard and click on add to enable your selection.

When does this make sense? Especially, when you’re using CRMs or other database relying applications it might make sense to exclude them to avoid data loss in case that you forgot to return to a specific application.

### Chapter 1-1 Enable Sleeping Tabs via Group Policy

The more company related approach would be to enable Sleeping Tabs via Group Policies.

You can find the specific setting to define sleeping tabs here:

```powershell
Computer Configuration > Administrative Templates > Microsoft Edge
```

Select Sleeping Tabs settings and set the policy to “Enabled“

## Chapter 2 – How to enable startup boost

To give your browser the required boost to startup, you can configure startup boost via the settings of your Edge browser:

```powershell
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge
```

Create a “DWORD” with the name “StartupBoostEnabled” and set it to “1”

## Chapter 3 – Conclusion

Microsoft have released a great way in Edge version 89 to optimize the browsing performance and experience in mutli-session environments. I can just highly encourage everyone to enable the above defined settings. I’m looking forward hearing your feedback about it!