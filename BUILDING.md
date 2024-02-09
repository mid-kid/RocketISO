Building
========

Doing this verbatim will never work for your setup. However, it helps provide an overview of how I'm using this set of scripts, and it serves as a reminder for myself.

Building an ISO
---------------

This copies the current system in / and its configuration to a new directory
and creates an ISO from that.

```
./genroot  # Installs @world into root/
./gencfg  # Installs relevant configurations from / into root/
./geniso  # Packs root/ and creates image.iso
```

Building a USB with permanent storage to test configuration
-----------------------------------------------------------

Same as building an ISO, except without baking the configuration into it. Using an overlay makes it quicker to start "from scratch" when testing `./gencfg` (though it supports incremental operation for most things), and makes things load quicker from the USB (squashfs is quite well optimized). Will only boot on UEFI systems.

```
./genroot  # Installs @world into root/
./geniso  # Packs root/ and creates image.iso
./genusb /dev/sdb  # Format drive, unpack image.iso into it, set up overlay
mount /dev/sdb2 /mnt
./gencfg_ovl /mnt/LiveOS/overlay-*/  # Installs configurations from / into overlay
umount /mnt
```

Creating a virtual disk image
-----------------------------

Similar to making a USB image, but a bit more manual. It's recommended to run these steps inside a "genenter" shell after rebuilding everything, as otherwise grub/the kernel might have trouble supporting virtualbox.
Just like genusb, requires UEFI to boot.

```
./genenter
cd /mnt/build
fallocate -f 20G disk.img
losetup /dev/loop0 disk.img
/mnt/scripts/genusb /dev/loop0
mount /dev/loop0p2 /mnt
/mnt/scripts/gencfg_ovl /mnt/LiveOS/overlay-*/
umount /mnt
losetup -d /dev/loop0
VBoxManage convertfromraw disk.img disk.vdi --format VDI
rm disk.img
```

Rebuilding everything to be portable
------------------------------------

My configuration is usually tailored for a specific (set of) system(s). For this reason, before being able to release an ISO with the processes above, I must modify the portage configuration and rebuild everything.

```
build=y ./genroot  # Installs @world (and bdeps, to save some time) into broot/
./genrepos  # Make a copy of the repositories to checkpoint, in repos/
./genportage  # Install and reconfigure /etc/portage into broot/
./genenter -c /mnt/scripts/genrebuild  # Install/remove pacakges and rebuild everything (takes forever!)

# Build a new rootfs and an iso out of it
./genenter -c 'final=y /mnt/scripts/genroot /mnt/build/root'
./genenter -c '/mnt/scripts/genportage /mnt/build/root'
./genenter -c '/mnt/scripts/geniso /mnt/build/{root,iso,image.iso}'

# Test the configuration changes and build a report
./gencfg_ovl |& tee gencfg.log
./ovldiff | tee gencfg.diff
# Optionally use genusb to test it with a USB

# Commit the changes and rebuild
rm -rf iso image.iso
./gencfg
./genenter -c '/mnt/scripts/geniso /mnt/build/{root,iso,image.iso}'
mksquashfs repos repos.squashfs
mksquashfs distfiles distfiles.squashfs
mksquashfs packages packages.squashfs
mksquashfs buildlogs buildlogs.squashfs
```
