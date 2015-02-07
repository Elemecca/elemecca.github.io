---
title: Unable to Enter BIOS Setup With Windows 8
date: 2012-11-16 20:34:19.000000000 -08:00
tags:
- troubleshooting
- Asus UX31A
- BIOS
- boot
- hardware
- hibernation
- Windows 8
---

I recently bought myself an [Asus ZenBook UX31A][zenbook].
I upgraded the OS to Windows 8 Pro. After I installed BIOS update 212,
I stopped being able to get into the BIOS setup menu. I would press F2
during the POST screen just like before, but it would just boot straight
into Windows. I'm pretty sure the BIOS update was a coincidence.

The culprit turned out to be Windows 8's new fast startup feature. When
you ask Windows to shut down it actually goes into a state similar to
hibernation so it can start back up more quickly. As far as the firmware
is concerned the system is hibernated, so it skips the normal boot path
and boots straight into Windows. The ZenBook's SSD means it boots plenty
fast already, so I just disabled fast startup. That's in the desktop
control panel under "Power Options". Click "Choose what the power
buttons do" in the left bar, then uncheck "Turn on fast startup" in the
bottom group of options. You may have to click "Change settings that are
currently unavailable" at the top of the window and go through a UAC
prompt first.

If you want to leave fast startup on, you can reboot into the BIOS setup
menu from the Windows 8 settings panel under "General", then "Advanced
Startup". As an aside, whose bright idea was it to have two completely
separate places you have to look for every setting? That's just silly.

[zenbook]: http://www.asus.com/Notebooks/Superior_Mobility/ASUS_ZENBOOK_UX31A/
