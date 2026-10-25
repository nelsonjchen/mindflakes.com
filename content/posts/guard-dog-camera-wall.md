---
title: "Guard Dog and Sentry: watching doggies with Reolink on Android TV and Mac"
date: 2026-10-03
draft: true
slug: "guard-dog-camera-wall"
tags:
  - android
  - macos
  - swift
  - reolink
  - home-assistant
  - video
  - codex
  - ai-assisted
---

The house I'm in has an extensive Reolink camera system, inside and outside. We're keeping an eye on three doggies as they move between those spaces. Reolink's desktop app didn't automatically switch to the cameras with activity, and I found it a bit crashy in my use. I wanted the views to follow wherever the dogs were hanging out without having to keep switching cameras myself.

So I built two sister apps with the assistance of Codex:

- [Guard Dog](#guard-dog), a Kotlin app for the living-room Android TV, gives everyone in the room a view of what's happening.
- [Sentry](#sentry), a native Swift app for my Mac, lets me watch the doggies while I'm working, including in Picture in Picture.

I supplied the goals and directed the work; Codex did the investigation and implementation described here. Codex also drafted this post.

## Choosing the views

Both apps use local Home Assistant activity events to choose which cameras to show, with a direct event connection for one standalone camera. Video goes straight from the cameras or NVR to the viewing app. Home Assistant supplies the activity information without carrying the video.

Up to four cameras appear at once. Already-visible cameras keep their positions when another view arrives, and a cleared detection gets twenty seconds of grace. When everything goes quiet, the last active camera stays on screen. A dog briefly falling out of the camera's detection zone shouldn't make the whole layout collapse and reform.

## Guard Dog on the TV {#guard-dog}

The TV is in the living room and can also be seen from the kitchen. I think of it a bit like the [information radiators][information-radiators] software teams use for sprint progress or build status. Ours shows three doggies. While Guard Dog is running, anyone in the room can glance over and see what's happening inside or outside. The camera selection needs to take care of itself for that to work.

Guard Dog excludes the living-room camera and the adjacent dining-table area from its activity selection. Those spaces are already right there in front of us. We don't need the TV to show us ourselves. That's the kind of tight customization I wanted: camera selection tailored to where the screen sits and how we use the room.

### Four views, two hardware decoders

Four little live views seemed reasonable. Then I found out that the Sony TV only had two shared hardware video-decoder slots.

Guard Dog uses Kotlin and Android's [MediaCodec][mediacodec]. Codex implemented a shared decoder budget: two visible streams can use hardware, with software decoding for the overflow. Four players independently asking for hardware kept running into the same limit.

Even software decoding had a surprise. On this Sony, its ordinary display-output path could consume a scarce video resource that the hardware panes needed. Codex had to use the vendor's graphics-output option for software codecs before an actual two-hardware, two-software grid would work. Testing on the TV mattered; the emulator couldn't establish how those Sony resources behaved.

The app gives hardware priority to sustained active views. Decoder changes wait for a keyframe, and resizing a pane keeps its connection and decoder running. A replacement codec can still cause a brief pause, but a layout change doesn't need to reconnect every surviving feed.

Both apps also keep a bounded pool of recent camera connections, so a camera can return to the screen without establishing a new connection each time.

A compatible single-view 4K HEVC feed needs the Sony's whole hardware block. The low-quality view moves to software while high quality starts offscreen, then high takes over once it has rendered. A second visible camera returns the app to the lower-quality grid.

Working within those limits was an interesting fight. For keeping an eye on the dogs across several rooms, I was happy to accept lower-resolution feeds and software decoding for extra views, with high quality available when a single compatible camera can have the hardware to itself.

## Sentry on the Mac {#sentry}

I wanted a Mac app so I could watch the doggies while I'm working. Sentry keeps their camera views alongside whatever else I'm doing, using Swift, VideoToolbox decoding, and Metal rendering.

### Taking the cameras into Picture in Picture

When I switch away from Sentry or minimize it, it can automatically open Picture in Picture. Returning to the app closes PiP. The floating view follows the selected cameras, including animal framing, using the existing decoded pictures without opening another set of camera streams.

Pause and sleep suspend playback and release the connections. The PiP implementation in this personal build uses Apple's private PIP framework, so it will need rechecking after macOS updates.

### Keeping a resting dog visible

A camera's motion event can clear while a dog is still resting in view. Sentry uses [Apple's animal recognition][animals] to periodically check the displayed frames for dogs and cats. Fresh recognition can keep that camera visible after the event clears. It can also sample recently viewed indoor feeds to find an animal again, reusing retained connections.

The recognition also helps with framing. Sentry can digitally crop around the animals so a small dog is easier to see in a large camera view. The crop includes surrounding context and expands when needed to keep multiple animals visible. Small changes in the detected boxes don't immediately jerk the picture around. Recognition runs locally on already-decoded frames.

Getting that framing right also means keeping detections attached to the right picture. The TV can use camera-supplied boxes on verified streams; the Mac can use both native camera metadata and Apple's recognition. Old boxes or coordinates from a different lens can put a crop in completely the wrong place.

## Made for this house

These are still personal apps tied to my camera configuration. Guard Dog also has Sony-specific playback fixes, and some TrackMix lens and TV relay work remains experimental.

I don't currently plan to release the code. It is tightly tailored to this house, these cameras, and the way I want to watch them. I wanted to write about it so people know this is something they could build for their own setup too: a viewer that follows their own priorities, on the screens they actually use.

I've also been [recovering readable C++ from Reolink's native SDK]({{< relref "reolink-arm64-recovery.md" >}}). Understanding its device communication and media handling could help with future work on both viewers and Home Assistant integrations.

Mostly, I wanted to stop operating a camera dashboard just to see where three dogs had gone. The two apps now fit the places we watch from: a shared TV view from the living room or kitchen, and a floating view beside my work on the Mac.

[mediacodec]: https://developer.android.com/reference/android/media/MediaCodec
[animals]: https://developer.apple.com/documentation/vision/vnrecognizeanimalsrequest
[information-radiators]: https://agilealliance.org/glossary/information-radiators/
