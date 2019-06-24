---
date: 2019-06-01T00:00:00-08:00
tags:
- azure_pipelines
- barrier
- apple
- bonjour
- windows
title: "Non-encumbered Windows Bonjour SDK DLLs and Libs"
---

This was one of those adventures where you find a problem and it snowballs from there.

As part of my work on the [Barrier project][barrier], we needed a way to build it with the [Bonjour SDK][bonjour_sdk] from an online CI/CD service. [Bonjour][bounjour] is used by Barrier to discover other barrier hosts on the same network. On Windows, the SDK files are needed. Other platforms natively come with the library or similar APIs already preinstalled.

But there's an issue. The SDK is behind a login wall on Apple's site. Rehosting it might be possible but it might violate some agreement within the installer. Luckily, the sources are open and are under very permissive licenses. 

To save some work, I reused the XBMC project's fork and made [my fork of the SDK][my-repo]. I didn't change any of XBMC's changes, if any. I added a CI system to build the necessary libraries in the same manner that Apple did. 

So, if you need a free, Cloud CI system accessible Apple Windows Bonjour SDK, look at the releases in [my repo][my-repo]! The library files are there and users can fork my build to build the stubs their own way as well. 

[barrier]: https://github.com/debauchee/barrier/
[boujour_sdk]: https://developer.apple.com/bonjour/
[bonjour]: https://en.wikipedia.org/wiki/Bonjour_(software)
[my-repo]: https://github.com/nelsonjchen/mDNSResponder