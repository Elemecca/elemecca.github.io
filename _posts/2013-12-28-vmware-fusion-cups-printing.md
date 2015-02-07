---
title: Sharing OS X Printers with Linux in VMware Fusion
date: 2013-12-28 07:09:07.000000000 -08:00
tags:
- hacks
- CUPS
- Mac OS X
- printing
- virtual machines
- VMware Fusion
---

VMware Fusion offers the ability to share OS X's printers with guest
OSes via its ThinPrint driver, but that often doesn't support the more
advanced options of business-grade printers and copy machines. Most
Linux distributions use the C UNIX Printing System (CUPS), which is also
used by OS X. CUPS is inherently networked even when working within a
single machine, so it's fairly simple to set up a Linux guest to print
to the OS X host's CUPS server.

Enable Network Access to CUPS
-----------------------------

By default, OS X's installation of CUPS is configured to only accept
connections from the local machine. In order to print to it from our
Linux guests we'll need to enable access from the VMware host-only
network. This is complicated somewhat by the fact that in OS X the CUPS
daemon does not run all the time; it's launched in an inetd-like manner
by OS X's launchd system. We therefore need to tell launchd to accept
connections from VMware guests first. Unfortunately, VMware Fusion
doesn't create its virtual network interfaces until a VM is started, so
we can't bind launchd to the actual interface address. Instead we'll
have launchd listen on all interfaces and configure CUPS to ignore
connections on other interfaces.

### Configure launchd

In a terminal, run  
`sudo nano /System/Library/LaunchDaemons/org.cups.cupsd.plist`

Find the section that looks like this:

    <dict>
        <key>SockNodeName</key>
        <string>127.0.0.1</string>
        <key>SockServiceName</key>
        <string>ipp</string>
    </dict>

Remove the `SockNodeName` property so it looks like this:

    <dict>
        <key>SockServiceName</key>
        <string>ipp</string>
    </dict>

Save the file by typing `Ctrl+X` and following the on-screen prompts.

### Configure CUPS

In a terminal, run   
`sudo nano /etc/cups/cupsd.conf`

First we need to make CUPS accept connections from clients on the VMware
host-only network. Find your VM's IP address on the host-only network.
I'll be something like `192.168.143.64`. Use the first three sections of
yours below if it's different from what's in the example.

Find the section that looks like this:

    <Location />
      Order allow,deny
    </Location>

Add `Allow` rules so it looks like this, remembering to substitute your
own network prefix if it's different:

    <Location />
      Order allow,deny
      Allow from @LOCAL
      Allow from 192.168.143.0/24
    </Location>

Next we need to allow remote clients to get the list of available
printers and which one is the default. In the `<Policy default>`
section, find the block that looks like this:

    <Limit Create-Job Print-Job Print-URI Validate-Job>
      Order deny,allow
    </Limit>

Add the `CUPS-Get-Printers` and `CUPS-Get-Default` operations to the
list. It should look like this:

    <Limit Create-Job Print-Job Print-URI Validate-Job CUPS-Get-Printers CUPS-Get-Default>
      Order deny,allow
    </Limit>

Save the file by typing `Ctrl+X` and following the on-screen prompts.

### Re-start CUPS

In a terminal, run   

`sudo launchctl unload /System/Library/LaunchDaemons/org.cups.cupsd.plist sudo launchctl load /System/Library/LaunchDaemons/org.cups.cupsd.plist`

Install the CUPS Client
-----------------------

Now that CUPS on your Mac is listening for connections from the VMware
network we can set up your Linux guest to use it. You will, of course,
first need to install the CUPS client library. On Debian and derived
distributions (including Ubuntu) you want the `cups-client` package,
plus the `cups-bsd` package for some command-line utilities:  
`sudo apt-get install cups-client cups-bsd`

On other distributions you'll need to find the appropriate packages for
yourself, but they'll usually be named somthing along the lines of
`cups-client` or `libcups`.

### Configure the CUPS Client

In a terminal on the guest, run  
`sudo nano /etc/cups/client.conf`

Remove any existing contents and replace them with:

    Encryption Never
    ServerName 192.168.143.1

Remember to use your own network prefix if it's different. Save the file
by typing `Ctrl+X` and following the on-screen prompts.

### Test the CUPS Client

In a terminal on the guest, run  
 `lpstat -t`

You should get a listing of all the printers configured on your Mac and
an indication of which is the default.
