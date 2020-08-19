---
title: Fooling pafish (part 2)
layout: post
category: post
---

# Why?
A common method of detecting VM's is by looking for the names of virtualized devices and other strings that giveaway the presence of a sandboxed environment.  If you followed my last post, you'll see that the vm doesnt trip any of pafish's QEMU checks.  However, this si because pafish is old and unmaintained.  If you look at the device manager in the VM you can clearly see devices branded "QEMU HARDDISK" etc.  The solution to this is patching the QEMU source code.

# Do it!
First remove the current QEMU from your system
```sh
sudo pacman  -Rsn qemu
```
<br/>

Then grab the files from [qemu-git in the AUR](https://aur.archlinux.org/packages/qemu-git/).  **Don't build and install from the AUR**.  Just get a snapshot and untar it.
<br/>

I might tar it up and put it on github but for now, add in the following patch to the PKGBUILD:
```sh
...

prepare() {
  cd "${srcdir}/${_gitname}"
  mkdir build-{full,headless}
  
    qemu_hd_replacement="WDC WD20EARS"
    qemu_dvd_replacement="DVD-ROM"
    hypervisor_string_replacemnt="\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0"
    sed -i "s/QEMU HARDDISK/$qemu_hd_replacement/g" hw/ide/core.c
    sed -i "s/QEMU HARDDISK/$qemu_hd_replacement/g" hw/scsi/scsi-disk.c
    sed -i "s/QEMU DVD-ROM/$qemu_dvd_replacement/g" hw/ide/core.c
    sed -i "s/QEMU DVD-ROM/$qemu_dvd_replacement/g" hw/ide/atapi.c
    sed -i "s/QEMU PenPartner tablet/<WOOT> PenPartner tablet/g" hw/usb/dev-wacom.c
    sed -i 's/s->vendor = g_strdup("QEMU");/s->vendor = g_strdup("<WOOT>");/g' hw/scsi/scsi-disk.c
    sed -i "s/QEMU CD-ROM/$qemu_dvd_replacement/g" hw/scsi/scsi-disk.c
    sed -i 's/padstr8(buf + 8, 8, "QEMU");/padstr8(buf + 8, 8, "<WOOT>");/g' hw/ide/atapi.c
    sed -i 's/QEMU MICRODRIVE/<WOOT> MICRODRIVE/g' hw/ide/core.c
    sed -i "s/KVMKVMKVM\\0\\0\\0/$hypervisor_string_replacemnt/g" target/i386/kvm.c
    sed -i 's/"bochs"/"<WOOT>"/g' block/bochs.c
    sed -i 's/"BOCHS "/"ALASKA"/g' include/hw/acpi/aml-build.h
    sed -i 's/Microsoft Hv/$hypervisor_string_replacemnt/g' target/i386/kvm.c
  mkdir -p extra-arch-{full,headless}/usr/{bin,share/qemu}

}

...
```
<br/>

Build and install. Double check you have appropriate MAKEFLAGS set in /etc/makepkg.conf or this could take hours:
```sh
makepkg -si
```
<br/>

# Final Results:
<img src=../assets/img/kvm/fooling-pafish/pafish-clear-1.png>
<br/>
<img src=../assets/img/kvm/fooling-pafish/pafish-clear-2.png>
<br/>
<img src=../assets/img/kvm/fooling-pafish/device-manager-qemu-strings.png>
<br/>

..now to compile Al-Khaser..
