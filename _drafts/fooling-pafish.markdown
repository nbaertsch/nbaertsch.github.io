---
title: Fooling pafish
layout: 
category: post
---

# Preamble
Recently, I built a new desktop computer, since Covid-19 has dictated that my upcoming Fall semester will be entirely held online. You can read more about the set-up in an upcoming post, but the long and short of it is that I went with an AMD build geared around hosting a homelab through KVM/QEMU/libvirt virtualization. I also passed through a dedicated RTX 2070s for gaming in a Windows 10 VM.  This presented a problem: some anti-cheat software is hostile to virtualized environments and will kick or even ban you for playing online in a virtual machine.  See [this tweet from BattlEye](https://twitter.com/TheBattlEye/status/1289027672186720263), the most recent AC to take a directly hostile stance towards VM gaming.  
<br/>

Now, there is a whole argument going on about the veracity of the numbers that BattlEye claims.  They are almost certainly lumping attempts at evading the VM detection together with actual cheating.  A large chunk of VFIO gamers, myself included, are required to do rudimentary detection evasion to get certain drivers to work ([error 43](https://wiki.archlinux.org/index.php/PCI_passthrough_via_OVMF#%22Error_43:_Driver_failed_to_load%22_on_Nvidia_GPUs_passed_to_Windows_VMs)).  They also failed to mention any intention of leveraging new technologies like AMD's [SEV-ES](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjTpODRrqHrAhWxr1kKHXZsBDMQFjAAegQIBxAB&url=https%3A%2F%2Fdeveloper.amd.com%2Fsev%2F&usg=AOvVaw1YbDnw3acmrI5EcfSWmsYn) for verifying the integrity of memory.
<br/>

# Disclaimer
For someone like myself who has zero interest in installing Windows natively, the only option to play certain games is to try to hide the fact that the machine is virtualized.  Before we begin, I have a disclaimer for anyone wanting to follow in my footsteps: it is always going to be easier to detect a vm than to hide it.  Researchers can only patch VM detection methods reflexively.  There are many differernt approaches to detecting a sandboxed environment and if you use the following methods to evade detection by anti-cheat, odds are good that they will eventually update their detection methods and you will have accounts banned.  I also want to make it clear that I am not an expert.  These methods were aggregated from various sources on the internet and I am simply documenting my experience(for myself because no-one reads this lol).
<br/>

I was inspired by [this reddit post](https://www.reddit.com/r/VFIO/comments/i071qx/spoof_and_make_your_vm_undetectable_no_more/) and am using the anti-detection methods mentioned there.
<br/>

# The Kernel - intercepting RDTSC exit calls
Recently a patch for the 5.7.11 kernel was released that intercepts RDTSC cpu exit calls. [This](https://github.com/SamuelTulach/BetterTiming) github repo has the patch for 5.7.11, but I'm on 5.8.1, so instead of downgrading the kernel I updated the patch which can be found [here](https://github.com/nbaertsch/BetterTiming/tree/5.8.1).
<br/>

Grab the 5.8.1 [linux kernel source code](https://www.kernel.org/):
```sh
wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.7.15.tar.xz
```
<br/>

Decompress:
```sh
xz -d linux-5.7.15.tar.xz
```
<br/>

Verify signature:
```sh
wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.7.15.tar.sign
```
```sh
gpg --verify linux-5.7.15.tar.sign
```
<br/>

Untar:
```sh
tar -xvf linux-5.8.1.tar
```
<br/>

Apply patch...

Use Arch default ``.config``:
```sh
zcat /proc/config.gz > .config
```
<br/>

Compile:
```sh
make -j16
```
<br/>

Install modules:
```sh
make modules_install 
```
<br/>

Copy the kernel to /boot:
```sh
cp -v arch/x86_64/boot/bzImage /boot/vmlinuz-linux58-patched
```
<br/>

Make initial ram disk using an edited mkinitcpio preset:
```sh
cp /etc/mkinitcpio.d/linux.preset /etc/mkinitcpio.d/linux58-patched.preset
vim /etc/mkinitcpio.d/linux58-patched.preset # <--change img names here to match new kernel
mkinitcpio -p linux58-patched
```
<br/>

Update GRUB config:
```sh
grub-mkconfig -o /boot/grub/grub.cfg
```
<br/>

Reboot the system and verify that your vm boots properly.

# Paranoid Fish
Paranoid Fish (pafish) is "a demonstration tool that employs several techniques to detect sandboxes and analysis environments in the same way as malware families do".  Check out the projects [github repo](https://github.com/a0rtega/pafish) for more info.

## Before KVM RDTSC patch


## After KVM RDTSC patch
<img src="../assets/img/kvm/pafish/pafish-kvm.png">
<br/>
