---
title: "My security camera tells me when the laundry is done"
date: 2026-09-29
draft: false
slug: "reolink-laundry-announcements"
tags:
  - home-assistant
  - reolink
  - automation
---

I wish Reolink did more with the speakers in its cameras. They're already powered, on the network, and sitting in useful places. They could double as little smart speakers for announcements, but that use doesn't seem to get much attention.

I'd already [opened an issue about adding speaker playback to the library behind Home Assistant's official Reolink integration][audiotalk-issue]. With AI coding tools like Codex around to help, I decided to take a crack at getting it working myself.

I wanted to know when the washer or dryer finished, even from across the house with the laundry-room door closed. There was already a Reolink camera with a speaker next to the stairs, near the bedrooms and a bit away from the machines. That seemed like a useful place for the announcements to come from.

First I needed Home Assistant to know when the appliances finished. Samsung had [announced a $4.99/month plan for SmartThings API access][smartthings-fee], with free access being phased out starting in October. I wasn't about to pay a stupid subscription for that. I used [LocalThings][localthings] instead: a free, open source integration that talks to the washer and dryer directly over the local network.

I still had to connect the appliances to SmartThings first to get LAN access working, and [keep them registered there][localthings-device-behavior]. LocalThings handles Home Assistant's ongoing communication with them locally.

The official Reolink integration still [lists two-way audio and text-to-speech as unavailable][official-limitations], so I used a custom integration.

I started with [ha-reolink-talk][upstream], Matthew Holm's integration based on [joeblack2k's Reolink Talk][original]. It already provides camera media players for audio files and text-to-speech, including cameras behind an NVR. I forked it to fix a couple of problems with my setup.

The first was camera discovery. A separate request to the NVR failed because of its self-signed HTTPS certificate. The error was caught, and discovery quietly fell back to channel 0. I'm still wondering how the hell this worked in the upstream author's setup without hitting that certificate error. Meanwhile, Home Assistant's official Reolink integration was already connected and knew which channels existed. Reusing that information got the right cameras to show up.

The audio framing needed fixing too. The encoded audio blocks weren't being packaged the way the camera expected. Correcting the block metadata, padding, and stream framing got speech playing through the camera. Those fixes are in [my fork][fork] and an [upstream pull request][pr].

Once playback worked, the laundry part was small. Each appliance's LocalThings progress sensor reaching `finish` triggers an announcement through the camera by the stairs. [Piper][piper] supplies the speech: “The washer has finished its cycle,” or the dryer equivalent.

I tried a few versions of the sound, including repeated flute announcements and a Samsung completion melody. With the appliance and the camera playing the Samsung melody out of sync, it was a confusing cacophony. I went with one short tone followed by speech instead. The washer gets a higher tone and the dryer a lower one. Each tone and its spoken message are rendered together into one audio file, so the camera only has to start playback once.

The laundry automations are specific to this house, but the integration fixes are there for other Reolink owners to use. A camera speaker is a pretty convenient little announcement speaker when it's already in the right place.

[upstream]: https://github.com/matthewholm/ha-reolink-talk
[original]: https://github.com/joeblack2k/reolink_talk
[fork]: https://github.com/nelsonjchen/ha-reolink-talk
[pr]: https://github.com/matthewholm/ha-reolink-talk/pull/1
[piper]: https://www.home-assistant.io/integrations/piper/
[official-limitations]: https://www.home-assistant.io/integrations/reolink/#known-limitations
[audiotalk-issue]: https://github.com/starkillerOG/reolink_aio/issues/161
[smartthings-fee]: https://blog.smartthings.com/smartthings-updates/a-new-enhanced-smartthings-api-experience/
[localthings]: https://github.com/mbillow/localthings
[localthings-device-behavior]: https://github.com/mbillow/localthings#known-device-behavior
