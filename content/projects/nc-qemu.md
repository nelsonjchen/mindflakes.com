---
title: "NC QEMU"
date: 2018-12-25
tags:
  - windows
  - virtualization
  - qemu
---

[![Build Status](https://dev.azure.com/nelsonjchen/NC%20QEMU/_apis/build/status/nelsonjchen.nc-qemu?branchName=master)](https://dev.azure.com/nelsonjchen/NC%20QEMU/_build/latest?definitionId=6?branchName=master)

## Links

* Azure Pipelines Build View: https://dev.azure.com/nelsonjchen/NC%20QEMU/_build  
* GitHub Repo: https://github.com/nelsonjchen/nc-qemu/
* **Releases Download Link: https://github.com/nelsonjchen/nc-qemu/releases**
  * Is this outdated and not matching the latest release of QEMU? Contact me.

## Changes

* Azure Pipeline to build `x86_x64` target QEMU in MSYS2 on Windows 
* Upgraded to Capstone with fixes that allow building in MSYS2. 

## About

"NC QEMU" is a very lightly augmented and experimental fork of QEMU to build on
Azure Pipelines and with [Windows Hypervisor Platform (WHPX)][whpx] support for
users who care the most about running the `x86_64` target fast. This build only
cares about the `x86_64` target and other targets are not built.

The output is a zip file with some DLLs and `qemu-system-x86_64.exe`. This QEMU
distribution can be run from a folder by itself. Alternatively,
`qemu-system-x86_64.exe` can be dropped into [QEMU for Window][qemu-win]'s
installation directory at `C:\Program Files\qemu`, replacing the existing
version. *Some features like USB network redirection might be missing though*.

The pipeline provides a differently compiled QEMU compared to [QEMU for
Windows][qemu-win]'s own [build instructions][qemu-win-build] which are
cross-compiled on Linux. Notably, NC QEMU is built on a Microsoft-provided copy
of Windows in MSYS2 with access to the Windows SDK headers for WHPX distributed
by Microsoft. The [MingW64 Toolkit][mingw64] which QEMU for Windows is built
with has unfortunately currently not reproduced the WHPX headers in a free
software manner. Users who want a QEMU for Windows with WHPX support but aren't
licensed nor want to accept the terms of installing the Windows 10 SDK are out
of luck. 

Another benefit is that this project provides a declaratively made and
executable pipeline that builds QEMU for Windows. Fork, setup an Azure Pipeline,
and adjust if needed. This can be considered a bit "executable documentation"
for building a QEMU x86_64 target of this nature. The build logs are public and
can be used as reference. For users having trouble building or configuring their
systems to build QEMU, this reproducible setup can be quite useful.

## Why QEMU+WHPX for `x86_64`

Absolutely not exhaustive:

* Acceleration is great. You can reach near-native speeds with acceleration. 
* WHPX is native to the OS. No foreign kernel drivers/modules/extensions needed.
* It's great for users who don't have access to [Intel's HAX][hax] because they
  either want Hyper-V and/or AMD support. With AMD CPUs being multi-core bargain
  monsters, developers and power users on AMD are becoming more numerous. 
* QEMU+WHPX boots Windows ISOs. [HAX currently does not.](https://github.com/intel/haxm/issues/20)
* QEMU is easier to hack on, script, and developed by many organizations, not
  one. WHPX support was *contributed by Microsoft* but is probably most useful for
  Google's QEMU-based Android emulators!
* Users hacking on or working with QEMU in Windows can bring their work to Linux
  KVM accelerated systems easier. And vice-versa.

## Packer WHPX workaround

The executables provided here do work for [Packer][packer-qemu]. Simply add the
directory `with qemu-system-x86_64.exe` to the `PATH`. Packer does not currently
recognize `whpx` as a valid argument for the `acceleration` key. The workaround
is to add it to `qemu-args` as a manual argument.

Additionally, the `cpu` argument of QEMU does not support `cpu=host` for `whpx`.
Specify something supported manually. 


## References

* Orbital (PS4 Emulator) Build instructions:
  https://github.com/AlexAltea/orbital/wiki/Building

[hax]: https://software.intel.com/en-us/articles/intel-hardware-accelerated-execution-manager-intel-haxm
[qemu-win]: https://qemu.weilnetz.de/ 
[qemu-win-build]: https://qemu.weilnetz.de/doc/BUILD.txt 
[whpx]: https://docs.microsoft.com/en-us/virtualization/api/
[packer-qemu]: https://www.packer.io/docs/builders/qemu.html
[mingw64]: https://mingw-w64.org/doku.php