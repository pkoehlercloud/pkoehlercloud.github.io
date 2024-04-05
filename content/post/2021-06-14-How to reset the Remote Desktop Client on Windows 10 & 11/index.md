+++
title = "How to reset the Remote Desktop Client on Windows 10 & 11"
image = "title.png"
date = 2021-06-14T16:56:47+06:00
draft = false
author = "Patrick Koehler"
tags = ["Windows 365", "Azure Virtual Desktop", "Microsoft Intune", "Azure", "Windows 11", "Windows 10"]
categories = ["Azure Virtual Desktop", "Windows", "Microsoft Azure"]
description = "A quick tip on how to reset the Remote Desktop client."
+++

Welcome to this very quick blog post about a topic that you may have experienced while enabling customers to use Azure Virtual Desktop. You installed the latest version on a computer and everything works fine, until the Remote Desktop app starts to crash frequently, or connections aren’t possible anymore. In some rare cases, you can’t even click anywhere.

For that reason, you can reset the Remote Desktop Client on Windows easily via PowerShell.

The feature is available from version 1.2.790, which has been released already in 2020.

Just run this small line of code and you’ll be fine again:

```powershell
cd "C:\Program Files\Remote Desktop\"
.\msrdcw.exe /reset /f
```

I hope this helps in case that you encounter that kind of issue one day.

Cheers,
Patrick