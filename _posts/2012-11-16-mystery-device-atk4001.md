---
title: Mystery Device ATK4001
date: 2012-11-16 21:43:14.000000000 -08:00
tags:
- troubleshooting
- Asus UX31A
- hardware
---

I just finished installing Windows 8 drivers for all the hardware in my
new laptop. One device in particular, `ACPI\VEN_ATK&DEV_4001`, stumped
me for quite a while. There's no open, centralized database for ACPI IDs
like there is for [PCI][pciids] and [USB][usbids]. All I knew was
that it was made by Asus (ATK is their vendor code) and searching Google
was turning up nothing. I eventually resorted to looking at the ini
files from random drivers on the Asus site for the UX31A until I found a
match. Apparently it's the wireless radio control interface, used to
enter airplane mode from the Windows UI. It's covered by the "ASUS
Wireless Radio Control" driver in the "Utilities" section of
[the download page][download].

[pciids]: http://pciids.sourceforge.net/ "pci.ids"
[usbids]: http://www.linux-usb.org/usb-ids.html "usb.ids"
[download]: http://www.asus.com/Notebooks_Ultrabooks/ASUS_ZENBOOK_UX31A/#support_Download
