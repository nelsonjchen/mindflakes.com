---
title: "Gargantuan Takeout Rocket"
date: 2022-04-16
tags:
  - google
  - takeout
  - azure
  - cloudflare
  - chrome
  - extensions
  - dataportability
  - backup
---

https://github.com/nelsonjchen/gtr

Gargantuan Takeout Rocket (GTR) is a toolkit of guides and software to help you take out your data from [Google Takeout][takeout] and put it somewhere else safe easily, periodically, and fast to make it easy to do the right thing of backing up your Google account and related services such as your YouTube account or Google Photos periodically.

Used to do this with a VPS. It was still too slow to backup 1.25TB. TOOK HOURS! Built a Rocket.

It is comprised of a two major components:

* A "GTR Proxy" Cloudflare Worker that HTTP3-izes the the Azure API and is capable of proxying base64 URLs to Google Takeout URLs.
* A browser extension that coordinates all this and constructs a job plan to execute.

These two components are infinitely scaleable and can be used to backup Google Takeout at breathtaking, unprecedented speeds.

The GTR repo contains a guide on how to setup all of this.

A future revision or successor may target S3 or S3-like APIs. In particular, can we target [Cloudflare's R2 when it comes out][r2]?

[takeout]: https://takeout.google.com/
[r2]: https://blog.cloudflare.com/introducing-r2-object-storage/
