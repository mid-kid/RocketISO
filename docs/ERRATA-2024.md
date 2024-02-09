Errata
======

This document describes things I want to fix in a future release, they're not specifically problems.

Bugs
----

* Kdenlive displays white icons on a white background
    * Fix: `kwriteconfig5 --file kdenliverc --group unmanaged --key force_breeze false`

Improvements
------------

* Use better compression for squashfs files, like xz for example.
* Figure out a better way to patch picom.conf in a way that makes it clear it's a live-exclusive setting.
* Set the hostname to RocketISO rather than PcoketRocket
* Set the resolution for `Virtual-1` display (qemu) as well, in files/live. Also 1280x800 is a better resolution if VirtualBox supports it.
