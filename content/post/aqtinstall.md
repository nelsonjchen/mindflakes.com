---
date: 2019-06-2T00:00:00-08:00
tags:
- qt
- azure_pipelines
- barrier
title: "Fast and lightweight headless Qt Installer from Qt Mirrors: aqtinstall"
---


As part of my work on the [Barrier project][barrier], we needed a way to build it with the [Qt installer]from an online CI/CD service. On some platforms, such as [Appveyor, Qt may be preinstalled][appveyor-software]. But on some other platforms such as [Azure Pipelines][ap], Qt may not be pre-installed. Nor on Travis. Nor on CircleCI. For Linux and macOS, there is a saving grace. The Qt libraries can be installed headlessly from their operating system repositories such as `apt` or `brew`. But this luxury does not exist as well on Windows. While Chocolatey does offer some Qt packages, they tend to ancient or non-official. 

There is a toolkit called [CuteCI][cuteci] that let you use the Qt Installer and script the GUI headlessly. It's janky and big though. Additionally, to pin versions, you must download 3 to 5 GB of Qt Installer to install a pinned version of Qt. 

So, I found this: https://lnj.gitlab.io/post/qli-installer/

Apparently it's possible to mimic the Qt Installer from a Python script. 

To save it from possible bit-rot, [I forked it to GitHub][my-repo]. I added parallel downloading, Windows support, and a CI system. I pushed Azure-Pipelines and each commit could cause 100 jobs to run. Across all the various platforms. Azure Pipelines did not break a sweat. 

[CuteCI][cuteci] is still extremely useful if you must use the official installer with official support. But the speeds with [my fork][my-repo] were insane. It would normally take many minutes to install Qt with CuteCI. My QLI-Installer fork? 10 or 20 seconds. 

*But it turns out there's an even better installer than mine!*

https://github.com/miurahr/aqtinstall/

`aqtinstall`

* Classy reusable Python structure
* On PyPi. You can do `pip install aqtinstall` and install `aqtinstall` for immediate use.

This is also another fork of `qli-installer`. The CI system was a in a bit of a sorry state though. So I contributed matrix generated jobs to test `aqtinstall` across Mac, Windows, and Linux. @miurahr was able to dial down the installation and testing madness too and ramp it up in some places. Actual Qt applications are built to test if `aqtinstall` work. Additionally, there was the insight that mobile platforms generally always require the latest Qt version. 

**So, if you want a fast and lightweight headless Qt installer that installs from Qt Mirrors and is continually tested, use `aqtinstall`!**

[cuteci]: https://github.com/hasboeuf/cuteci
[barrier]: https://github.com/debauchee/barrier/
[ap]: https://azure.microsoft.com/en-us/services/devops/pipelines/
[appveyor-software]: https://www.appveyor.com/docs/windows-images-software/
[my-repo]: https://github.com/nelsonjchen/qli-installer