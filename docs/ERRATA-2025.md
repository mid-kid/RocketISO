Errata
======

This document describes things I want to fix in a future release, they're not specifically problems.

Bugs
----

* No drivers for sys-apps/pcsc-lite (e.g. app-crypt/ccid) are installed. NFC and card readers won't work.
* Power menu on polybar is broken (I haven't touched it in at least 3 years apparently...)
* Kdenlive looks broken
    * Fix: Settings/Arranjament -> Color scheme/Esquema de color -> Default/Predeterminat
* Kernel was compiled without the optimizations in /etc/portage/kernel/config.d. This makes it significantly slower on some machines.

Improvements
------------

(I didn't improve on a few things from last year, building the ISO at all took me long enough)

* Add a README button to the polybar, which goes away after clicking.
* Install `xlivebg` for fun (if still using X11)
* Install `ponysay` for fun
* Add a few ROMs for the provided emulators.
* Reconsider the language and keyboard settings:
    * Add a boot menu option for the "defaults"?
