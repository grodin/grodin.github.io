---
layout: post
title: "Raspbian partition shenanigans"
description: ""
category: 
tags: raspi
---
This post is a summary of all the stuff related to moving the root partition to a USB drive and what to do if/when the boot partition gets corrupted in this situation.

####Moving root partition

This is straightforward, just the normal procedure for copying a partition 
from one device to another. Various options include:

1. use `dd` to just copy the partitions (not reccomended, simply because it will require some calculation of offsets)

2. create a new `ext4` partition on the new device and copy everything across, probably using `rsync`

3. I think `gparted` allows you to do this from a gui, but I can't remember precisely. In any case this is basically the same as option 2 above.

The only thing left to do is to tell the raspi to use this new partition. The way to do this is to edit the file `/boot/cmdline.txt` and change it to look like this:

    dwc_otg.lpm_enable=0 console=ttyAMA0,115200 console=tty1 root=/dev/sda3 rootfstype=ext4 elevator=deadline rootwait rootdelay=5

i.e., change `root=/dev/whatever` to `root=/dev/new-partition-device`, _making sure to check which partition really is the new root partition_ and also adding the `rootdelay=5` option in case the USB device is a bit slow to come online.

That's it!

####Boot partition corruption

When/if the boot partition gets corrupted, and you're running the filesystem from a USB device, it's useful to be able to re-image only the boot partition from a Raspbian image. To do this, `kpartx` is very useful.

What `kpartx -av someimage.img` does is to create a new loop device and then link all the partitions in the image file to partitions in the loop device. In effect, you end up with devices `/dev/mapper/loop0p1`, `/dev/mapper/loop0p2`, etc. which are simply the partitions in the image file. You can then mount these, or just `dd` them to your SD card boot partition.

Another useful thing is to make an image of your boot partition at regular intervals, so you can simply re-image when necessary rather than starting again with the default raspbian boot partition.

Hopefully I won't need to this again for a while, but now when I do, at least I've got an offline reference!