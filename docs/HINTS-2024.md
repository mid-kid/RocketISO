Hints
=====

This document lists some interesting things you can do with this live system. Some of it is unique to this system, some of it is just general linux stuff.

Make sure you didn't miss the banners, and welcome messages. Use Super+d and read the [README](https://htmlpreview.github.io/?https://raw.githubusercontent.com/mid-kid/RocketISO/master/docs/README-2024.html)!

* The basics:
    * `sl`
    * `btop`
    * `neofetch`
* `firefox about:addons`
* Look through the application menu (Super+d) for some games
* Create a QR code and view it: `qrencode foobar -s 10 -o -|display`
    * Scan it by pressing the "Print Screen" key
* Create some art: `pamcrater|pamshadedrelief|pamrecolor --colorspace=pal|pamscale 2|pnmrotate 45|pamoil|pamcut 256{,,,}|pamx`
* Create some work: `echo "digraph{suck->swallow}"|dot -T png|display`
* Expose current directory on http: `webfsd -d`
* Make touchpad input absolute: `xinput set-mode 'SynPS/2 Synaptics TouchPad' ABSOLUTE`
    * ~~play Osu! like this~~
* `mpv 'https://stream.r-a-d.io/main.mp3'`

"fun" was unfortunately a bit of an afterthought for this release, as I'm now realising I can do way better.
