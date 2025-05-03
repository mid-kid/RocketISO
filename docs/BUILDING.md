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
fallocate -l 20G disk.img
losetup /dev/loop0 disk.img
./genusb /dev/loop0
mount /dev/loop0p2 /mnt
./gencfg_ovl /mnt/LiveOS/overlay-*/
umount /mnt
losetup -d /dev/loop0
VBoxManage convertfromraw disk.img disk.vdi --format VDI
```

It may also be tested in QEMU:
```
qemu-system-x86_64 -enable-kvm \
    -m 4G \
    -drive file=disk.img,format=raw \
    -cpu host,topoext=on \
    -smp cores=4,threads=2 \
    -device virtio-vga-gl \
    -display gtk,gl=on \
    -audiodev alsa,id=snd0 \
    -device virtio-sound-pci,audiodev=snd0 \
    -bios /usr/share/edk2/OvmfX64/OVMF_CODE.fd
```

Rebuilding everything to be portable
------------------------------------

My configuration is usually tailored for a specific (set of) system(s). For this reason, before being able to release an ISO with the processes above, I must modify the portage configuration and rebuild everything.

This is the sequence of commands I use to make the final release.

```
./genrepos  # Make a repository snapshot, in repos.squashfs

# Create build environment and rebuild everything
build=y ./genroot  # Installs @world (and bdeps) into broot/
./genportage  # Install and reconfigure /etc/portage into broot/
./genenter -c /mnt/scripts/genrebuild  # Install/remove packages and rebuild everything (takes forever!)

# Build a new rootfs and an iso out of it
./genenter -c 'final=y /mnt/scripts/genroot /mnt/build/root'
./genenter -c '/mnt/scripts/genportage /mnt/build/root'
./genenter -c '/mnt/scripts/geniso /mnt/build/{root,iso,image.iso}'

# Test the configuration changes and build a report
./gencfg_ovl |& tee gencfg.log
./ovldiff | tee gencfg.diff
# Optionally use genusb to test it with a USB

# Commit the changes and rebuild ISO
./genenter -c '/mnt/scripts/ovlpack /mnt/build/iso/LiveOS/overlay'
mv iso/LiveOS/overlay.img iso/LiveOS/squashfs.img
rm -rf iso/LiveOS/{overlay,ovlwork} image.iso
./genenter -c '/mnt/scripts/geniso /mnt/build/{root,iso,image.iso}'
mksquashfs distfiles distfiles.squashfs -comp xz
mksquashfs packages packages.squashfs -comp xz
mksquashfs buildlogs buildlogs.squashfs -comp xz
mksquashfs data data.squashfs -comp xz
```
