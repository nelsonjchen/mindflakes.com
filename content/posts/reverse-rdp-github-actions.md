---
title: "Reverse RDP into Windows on GitHub Actions"
date: 2019-12-07T17:24:55Z
tags:
  - githubactions
  - devops
---

Ever wonder what the Desktop of the Windows Runners on GitHub Actions looks like?

Or perhaps you're missing the ability to [RDP into build agents like on Appveyor].

I wrote some steps that use ngrok to make a reverse tunnel possible. I also turned on RDP if it wasn't on already and set a password.

Take a look here!

https://github.com/nelsonjchen/reverse-rdp-windows-github-actions

![GitHub Actions Desktop](/images/reverse-rdp-desktop.png)

[RDP into build agents like on Appveyor]: https://www.appveyor.com/docs/how-to/rdp-to-build-worker/
