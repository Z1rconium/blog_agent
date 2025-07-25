---
layout: post
title: "Enabling KVM GPU Passthrough"
date: 2025-04-27 00:00:00 +0800
categories: [virtualization]
tags: [kvm, gpu, linux]
---

In this article I describe how to set up GPU passthrough on a Linux host using
KVM and QEMU.  My goal was to run a Windows virtual machine with near‑native
graphics performance so that I could game and use GPU‑accelerated applications
without dual booting.

## Enabling IOMMU

The first step is to enable **I/O Memory Management Unit (IOMMU)** support on
the host.  In the BIOS or UEFI settings, look for options such as "SVM
Mode" (on AMD systems) or "VT‑d" (on Intel systems) and make sure they are
enabled.  Without IOMMU the host cannot isolate PCIe
devices for exclusive use by a guest, which is essential for GPU passthrough.

## Updating GRUB Parameters

After enabling IOMMU in firmware, update the kernel boot parameters in
`/etc/default/grub` to activate the necessary virtualization features.  For
example, add the following options to the `GRUB_CMDLINE_LINUX_DEFAULT`
variable:

```bash
amd_iommu=on iommu=pt kvm_amd.npt=1 video=efifb:off
```

Then rebuild the GRUB configuration and reboot.  These parameters turn on
IOMMU, pass through the GPU without allowing the host to allocate pages from
it and disable the EFI framebuffer to prevent conflicts.

## Binding the GPU to VFIO

Once the system boots, identify the PCI address of your GPU and bind it to the
`vfio-pci` driver.  You can accomplish this by creating a file in
`/etc/modprobe.d` that lists the vendor and device IDs of your GPU.  Upon
loading, the VFIO driver will claim the GPU and make it available to QEMU.

After these preparations, create a libvirt or QEMU virtual machine and assign
the GPU device.  When configured correctly, Windows should recognise the
graphics card and install the appropriate drivers, giving you performance
similar to bare metal.  For additional details, consult the original article on
the old site.
