---
title: "Running Windows XP using Libvirt inside Ubuntu inside VMWare Fusion"
created_at_time: 18:09:02 -0800
---

So I'm investigating running Windows XP inside `libvirt` in Ubuntu on my MacBook for continuous integration testing
purposes with Jenkins. I tried for many hours to get it working but Windows would not boot up or it would get stuck
on the `NTLDR cannot be found` issue. As it turns out, I did not enable "Enable hypervisor applications in this
virtual machine". I was under the assumption that I would not need to check that box as I wrongly assumed `qemu`
would handle all the needed machine translation and that an error with bootup is not the fault of lacking the ability
to use a CPU feature to emulate something or the disk emulation being borked. With five different ISOs of various
pedigrees, I tried installing Windows XP with `virt-manager` and all of them failed to bootup with various disk image
formats like `qcow2` or `raw`. It was only until I checked that checkbox did it then work.

That's my surprise for today. I really wished I checked that checkbox earlier! It determines if your machine can
bootup and only using `qemu` without `kvm` will not cut it for booting Windows XP inside libvirt inside Ubuntu inside
VMWare Fusion.
