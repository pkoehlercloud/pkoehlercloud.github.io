---
title: "FSLogix and Microsoft 365 Apps for Enterprise Activation issue remediation"
image: "title.png"
date: 2020-12-08T16:56:47+06:00
draft: false
author: "Patrick Koehler"
tags: ["Windows 365", "Azure Virtual Desktop", "MSIX", "MSIX App Attach", "Azure", "Windows 11", "Windows 10"]
categories: ["EUC", "Cloud Service","AVD" , "Azure Virtual Desktop", "Edge", "Azure", "MSIX"]
---

Last week I faced an issue at the client site while getting a Microsoft 365 Apps for Enterprise Activation notification after the user login.

In the menu bar in the center top we can see Word [Non-Commercial Use] [Unlicensed Product]

Office has worked for the last couple of months perfectly on WVD, so I had to investigate the issue and obviously, the first thing I’ve tried, is to Sign in again to see the application behavior. Finally, the screen was flickering a few times, but the error message persists and Office was simply unusable for all users on the system.

So first of all I started to analyze the base parameters like:

* FSLogix version used (it was 2.9.7349.30108 at the time of doc creation)
* Office version used (shown in the screenshot below)
* Office update channel in use (shown in the screenshot below)
* Roaming license token for Microsoft Apps in place

I quickly realized that the client updated the image and changed the Update Channel from Semi-Annual channel to Current Channel.

This is in general not a big issue, but you need to make sure that Office is always in it’s latest version, which was not the case in this scenario!

### Tip 1: If you need to choose Current Channel, make sure Office is in it’s latest version!

But this didn’t solve the problem at all, it just brought us to the latest version. So the next thing to check is, what happened with our existing License Token for Microsoft Apps for Enterprise.

Usually, when using FSLogix Profile and Office365 Container, we have the possibility and include the Office Activation Data inside the Office365 Container. This can be defined easily through Group Policies.

If thats the case for you, you’ll find the activation token as if you’re working with local profiles underneath the following location:

<b> %LOCALAPPDATA%\Microsoft\Office\16.0 </b>

And it turned out that no files were present here! This was our starting point. Next, we need to ensure, that all user-related information is removed from the registry and that the Work or School Account settings are reset for the user to be able to renew the activation token.

For that reason, I’ve created a tool, which performs all of these tasks for you automatically and it can be used for one-shot remediation.

Please find the video and the tool download afterwards:
<iframe width="560" height="315" src="https://www.youtube.com/embed/Pflz2Cdp0w8?si=9ZfCsrwc5vqflwTs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

The tool is free usable and can be downloaded from my GitHub repository:
(https://github.com/wvdlogix/M365TroubleshooterWVD)

Finally, you can start one of your office apps again and you just need to click on accept license agreement, like it’s your first time starting office. Outlook data and configuration settings won’t be lost!
