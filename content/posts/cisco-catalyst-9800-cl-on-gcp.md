---
title: "Cisco Catalyst 9800-CL on GCP: what a fight"
date: 2026-03-20T13:15:00-08:00
tags:
  - cisco
  - catalyst-9800
  - gcp
  - wireless
  - homelab
  - ai-assisted
  - codex
---

I finally wrote up my notes on getting a modern [Cisco Catalyst 9800-CL](https://www.cisco.com/c/en/us/products/wireless/catalyst-9800-cl-wireless-controller-cloud/index.html) running on [Google Cloud Platform](https://cloud.google.com/).

This was my first time using Codex to bring up a GCP setup from scratch. I supplied the goals and directed the work; Codex did essentially all of the technical investigation, image modifications, and scripting to get the controller working. Codex also drafted this post.

The short version is that this was much more of a fight than I expected.

Cisco's [public GCP-facing material](https://www.cisco.com/c/en/us/products/collateral/wireless/catalyst-9800-cl-wireless-controller-cloud/nb-06-cat9800-cl-cloud-wirel-data-sheet-ctp-en.pdf) still nudges people toward [Google Cloud Marketplace](https://www.cisco.com/c/dam/en/us/td/docs/wireless/controller/9800/9800-cloud/deployment/c9800-cl-gcp-deployment-guide.pdf), but the Marketplace offerings I found were outrageously old: `16.12.1` and `16.12.2s` from the `2019-2020` era, plus `17.2.1` and `17.3.5a` from the `2020-2021` era.

That is a pretty bad situation if what you actually want is a modern WLC on GCP. The version I actually worked from was `17.15.04d`, which Cisco's software portal lists with a release date of `19-Dec-2025`. Cisco's public guidance still points at a path that appears stuck on software families from years ago, and it was not a good starting point for getting a current controller running.

Getting a newer image directly from Cisco's site did not end the fight. That image's GCP support was broken too, and Codex ended up ripping into it to figure out what the hell was going wrong. Under my direction, it investigated the boot path and worked through the image modifications needed to get the controller running. This was much more than asking it to write a few deployment commands.

I have to give Codex credit for sticking with that stuff. Ripping apart a broken appliance image is nasty, patient trial-and-error work, especially when GCP is involved. Finding out whether a change worked can take minutes, and getting somewhere can take many attempts. Codex also configured serial logging and read through the serial logs to figure out what was happening during boot and what to try next. It kept working through that slow feedback loop. That persistence impressed me as much as the technical work itself.

I put the actual handoff-style guide here:

- [Cisco Catalyst 9800-CL on GCP guide](https://github.com/nelsonjchen/c9800-cl-gcp-setup)

That guide covers:

- where to get the `qcow2`
- why Marketplace is not a great baseline right now
- what actually worked to get the controller reachable
- how I validated it was really up
- how to adopt an AP into the supported public-cloud path

One of the main conclusions is that Cisco's built-in GCP bootstrap path seems to create real first-boot state that the appliance expects, which helps explain why simply editing `/varied/iosxe_config.txt` offline was not enough on a pristine raw image.

Anyway: what a fight. But at least the notes are in one place now.
