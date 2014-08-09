---
layout: post
title: "Sharing mobile broadband over Wifi"
description: ""
category: 
tags: [raspberry-pi]
---

This post is to document exactly what I did in setting up my Raspberry Pi B as
(sort-of) a wireless router to share a mobile broadband connection.

## Requirements ##

1. Raspberry Pi running Raspbian
2. Mobile broadband dongle
3. Wifi dongle which supports master mode (AP mode)

## Steps ##

Assuming that the Raspberry Pi is already running Raspbian, the steps are:

1. Install the packages `ppp`, `hostapd` and `udhcpd` (this step requires an 
   internet connection already available, so you can't bootstrap this setup)
2. Download to the Raspberry Pi the very useful `sakis3g` script/tool.
3. Test that it is possible to make an internet connection using the broadband
   dongle and `sakis3g` in interactive mode.
4. Configure `/etc/network/interfaces` so the wifi dongle gets a fixed ip.
5. Edit `/etc/default/ifplugd` so that the ip address assigned above won't
   keep being lost.
6. Edit `/etc/sudoers` (using `visudo`) to allow `sakis3g` to run as root
   without requiring password.
7. Write `/etc/sakis3g.conf` file.
8. Write `/etc/hostapd.conf` file and allow `hostapd` to be run at boot.
9. Write `/etc/udhcpd.conf` file and allow `udhcpd` to be run at boot.
10. Whenever you want to use the mobile broadband connection, `ssh` into the
    Pi and run `sakis3g connect`. This could also be setup to run at boot.
