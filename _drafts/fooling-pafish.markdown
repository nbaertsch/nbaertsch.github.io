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
Patch the 5.7.11 kernel according to [this](https://github.com/SamuelTulach/BetterTiming) github repo.  The latest kernel (5.8.1) does not work with these changes (I will discuss more below)
<br/>

Grab the 5.7 [linux kernel source code](https://www.kernel.org/) (5.7.15 in my case):
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

The original project has a patch file for 5.7.11 but I am using 5.7.15.  I manually applied the changes in the patch and made my own patchfile but :
```
diff --color -Naur linux-5.7.15/arch/x86/kvm/svm/svm.c linux-5.7.15_patched/arch/x86/kvm/svm/svm.c
--- linux-5.7.15/arch/x86/kvm/svm/svm.c	2020-08-11 09:35:42.000000000 -0400
+++ linux-5.7.15_patched/arch/x86/kvm/svm/svm.c	2020-08-17 03:57:45.676940918 -0400
@@ -1041,6 +1041,7 @@
 	set_intercept(svm, INTERCEPT_XSETBV);
 	set_intercept(svm, INTERCEPT_RDPRU);
 	set_intercept(svm, INTERCEPT_RSM);
+	set_intercept(svm, INTERCEPT_RDTSC);
 
 	if (!kvm_mwait_in_guest(svm->vcpu.kvm)) {
 		set_intercept(svm, INTERCEPT_MONITOR);
@@ -2143,6 +2144,7 @@
 
 static int cpuid_interception(struct vcpu_svm *svm)
 {
+	svm->vcpu.run->exit_reason = 123;
 	return kvm_emulate_cpuid(&svm->vcpu);
 }
 
@@ -2706,6 +2708,26 @@
 	return nop_interception(svm);
 }
 
+static int handle_rdtsc_interception(struct vcpu_svm *svm)
+{
+	u64 differece;
+	u64 final_time;
+	u64 data;
+
+	differece = rdtsc() - svm->vcpu.last_exit_start;
+	final_time = svm->vcpu.total_exit_time + differece;
+
+	data = rdtsc() - final_time;
+
+	svm->vcpu.run->exit_reason = 123;
+	svm->vcpu.arch.regs[VCPU_REGS_RAX] = data & -1u;
+	svm->vcpu.arch.regs[VCPU_REGS_RDX] = (data >> 32) & -1u;
+
+	skip_emulated_instruction(&svm->vcpu);
+
+	return 1;
+}
+
 static int (*const svm_exit_handlers[])(struct vcpu_svm *svm) = {
 	[SVM_EXIT_READ_CR0]			= cr_interception,
 	[SVM_EXIT_READ_CR3]			= cr_interception,
@@ -2772,6 +2794,7 @@
 	[SVM_EXIT_RSM]                          = rsm_interception,
 	[SVM_EXIT_AVIC_INCOMPLETE_IPI]		= avic_incomplete_ipi_interception,
 	[SVM_EXIT_AVIC_UNACCELERATED_ACCESS]	= avic_unaccelerated_access_interception,
+	[SVM_EXIT_RDTSC]			= handle_rdtsc_interception,
 };
 
 static void dump_vmcb(struct kvm_vcpu *vcpu)
diff --color -Naur linux-5.7.15/arch/x86/kvm/x86.c linux-5.7.15_patched/arch/x86/kvm/x86.c
--- linux-5.7.15/arch/x86/kvm/x86.c	2020-08-11 09:35:42.000000000 -0400
+++ linux-5.7.15_patched/arch/x86/kvm/x86.c	2020-08-17 04:01:42.776940150 -0400
@@ -8203,7 +8203,7 @@
  * exiting to the userspace.  Otherwise, the value will be returned to the
  * userspace.
  */
-static int vcpu_enter_guest(struct kvm_vcpu *vcpu)
+static int vcpu_enter_guest_real(struct kvm_vcpu *vcpu)
 {
 	int r;
 	bool req_int_win =
@@ -8512,6 +8512,25 @@
 	return r;
 }
 
+static int vcpu_enter_guest(struct kvm_vcpu *vcpu)
+{
+	int result;
+	u64 differece;
+
+	vcpu->last_exit_start = rdtsc();
+
+	result = vcpu_enter_guest_real(vcpu);
+
+	if (vcpu->run->exit_reason == 123)
+	{
+		differece = rdtsc() - vcpu->last_exit_start;
+		vcpu->total_exit_time += differece;
+	}
+
+	return result;
+}
+
+
 static inline int vcpu_block(struct kvm *kvm, struct kvm_vcpu *vcpu)
 {
 	if (!kvm_arch_vcpu_runnable(vcpu) &&
diff --color -Naur linux-5.7.15/include/linux/kvm_host.h linux-5.7.15_patched/include/linux/kvm_host.h
--- linux-5.7.15/include/linux/kvm_host.h	2020-08-11 09:35:42.000000000 -0400
+++ linux-5.7.15_patched/include/linux/kvm_host.h	2020-08-17 04:03:28.656939807 -0400
@@ -319,6 +319,9 @@
 	bool ready;
 	struct kvm_vcpu_arch arch;
 	struct dentry *debugfs_dentry;
+
+	u64 last_exit_start;
+	u64 total_exit_time;
 };
 
 static inline int kvm_vcpu_exiting_guest_mode(struct kvm_vcpu *vcpu)

```
<br/>

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
vim /etc/mkinitcpio.d/linux58-patched.preset // <--change img names here to match new kernel
mkinitcpio -p linux58-patched
```
<br/>

Update GRUB config:
```sh
grub-mkconfig -o /boot/grub/grub.cfg
```
<br/>

Reboot the system and hope you didn't fuck anything up!

# Paranoid Fish
Paranoid Fish (pafish) is "a demonstration tool that employs several techniques to detect sandboxes and analysis environments in the same way as malware families do".  Check out the projects [github repo](https://github.com/a0rtega/pafish).
<br/>
