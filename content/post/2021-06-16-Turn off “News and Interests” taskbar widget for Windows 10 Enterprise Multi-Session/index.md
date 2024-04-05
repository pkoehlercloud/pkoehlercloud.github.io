---
title: "Turn off “News and Interests” taskbar widget for Windows 10 Enterprise Multi-Session"
description: "meta description"
image: "title.png"
date: 2021-07-29T16:56:47+06:00
draft: false
author: "Patrick Koehler"
tags: ["Windows 365", "Azure Virtual Desktop", "Microsoft Intune", "Endpoint Manager"]
categories: ["Azure Virtual Desktop", "Windows"]
---

A feature that came with the latest Windows Update is the “News and Interests” taskbar widget for Windows 10. A feature that might make sense for the user experience on stationary PC’s is definetly something that can confuse users in multi-session environments – like such on Azure Virtual Desktop.

![alt text](image.png)

How you can easily turn this feature off is what we’re covering in today’s blog post!

## Turn off the feature via Group Policy / Registry

The easiest way I would recommend deactivating the features for all users is via Group Policy. As there is currently no official option to turn this feature off, you can achieve this by using a registry key.

Navigate to the Group Policy Management console on your Active Directory server and create a new Registry Key for your Session Hosts:

```powershell
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Feeds
```

Create a new entry:

```powershell
Type: DWORD
Name: ShellFeedsTaskbarViewMode
Value: 2 
```

<b>IMPORTANT! Value “2” deactivates the feature in the next couple of minutes from the virtual machine. If the value is set to “0”, the feature remains enabled!</b>

That’s it! With that trick you can deactivate the widget easily.

Nevertheless I would be interested to know your opinion about the feature! Do you any sense in keeping it enabled on multi-session VMs? Let me know!

Cheers,
Patrick