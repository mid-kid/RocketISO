Errata
======

This document describes things I want to fix in a future release, they're not specifically problems.

Bugs
----

* Kdenlive displays white icons on a white background
    * Fix: `kwriteconfig5 --file kdenliverc --group unmanaged --key force_breeze false`
* `w3m` is not installed (it's a BDEPEND on my system), causing image previews in `ranger` to not work. Consider using `:shell nsxiv -t .` to view images in the current folder instead.

Improvements
------------

* Use better compression for squashfs files, like xz for example.
* Figure out a better way to patch `~/.config/picom/picom.conf` in a way that makes it clear it's a live-exclusive setting.
* Set the hostname to RocketISO rather than PcoketRocket
* Set the resolution for `Virtual-1` display (qemu) as well, in `files/live`. Also 1280x800 is a better resolution if VirtualBox supports it.
