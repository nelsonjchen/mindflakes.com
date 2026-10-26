---
title: "Building an Android app that talks about my bike radar"
date: 2026-10-03
draft: true
slug: "ridewatch-bike-radar"
tags:
  - android
  - cycling
  - bluetooth
  - codex
---

I use a [Garmin Varia RVR315](https://www.garmin.com/en-US/p/669024/) bike radar while riding my scooter. The radar clips onto the back of my CamelBak with an [EFEALL strap mount][radar-clip], and my phone stays in my pocket. I'm not a fan of mounting the phone on the scooter, so I wanted spoken information about vehicles approaching from behind without having to look at a screen.

The radar sends its detections over Bluetooth. I also have the recurring problem of Garmin's Android app stopping in the background and me forgetting to open it again. I wanted clearer connection behavior as well as the spoken alerts.

The clip is sold as a saddle-bag strap mount with a Garmin quarter-turn fitting, including support for the RVR315. It can rotate and tilt and comes with a safety tether. I'm using it on my CamelBak instead of a bike's saddle bag.

That turned into RideWatch, a standalone Android app I'm building with Codex helping with the research and implementation. It's still a prototype, but I've been using it on scooter rides.

It's neat how much I can follow by listening. I can tell when a car behind me is slowing down to pull over or park, or when one is approaching very quickly. Those changing alerts give me a sense of what's happening behind me while my phone stays in my pocket.

The car count is useful too. If I'm waiting to make a U-turn and it announces two cars behind me, I know I'm waiting for two cars to pass. After the first one goes by, there's still another one to account for. My phone can stay in my pocket through all of this.

None of this replaces looking, listening, and taking the usual precautions. Hearing two cars doesn't mean there can't be a third, and silence isn't an all-clear. It's an extra source of awareness while riding, and I've found it really useful in that role.

The first job was finding out what the radar actually sends. Public work on other Garmin radars provided leads, but a newer model's packet layout isn't automatically the RVR315's layout. I inspected the Android Varia app to trace its Bluetooth subscriptions and legacy decoder, then wrote a separate implementation. Garmin's code and sound recordings aren't bundled into RideWatch.

The app now has a graphical lane showing detected vehicles, threat changes, synthesized chimes, and optional speech. The speech is deliberately brief: numbers rather than a full sentence repeated every second. For multiple vehicles, it can also announce changes in the count. An old queued announcement needs to expire when the underlying situation changes; speaking stale information clearly is still speaking stale information.

The speed part remains experimental. Closing speed relative to me on the scooter and a car's speed along the road are different things. RideWatch can combine the decoded value with a recent phone estimate of rider speed, but the interpretation needs a controlled vehicle pass and reference measurements. Recovering arithmetic from an app isn't the same as validating that estimate on the road.

Getting the first physical connection working produced a wonderfully mundane bug. Android's companion-device API returned a Bluetooth address with lowercase hexadecimal letters. The Bluetooth parser used for the next step expected uppercase. The app failed before it even tried to connect to the radar.

Normalizing saved addresses, including ones from earlier installs, fixed that. My Pixel then showed the radar's battery reading and received a live stream with no targets. That established a real connection and decoded notifications. It didn't establish vehicle range, speed accuracy, or alert timing.

The fix for keeping RideWatch running in the background is a persistent notification. While monitoring is enabled, the app keeps an Android foreground service running with that notification, even after I leave the app and haven't opened it for a while. The version of Garmin's app I have doesn't keep a notification going like this. Having RideWatch stay active between uses addresses one of the main reasons I wanted to build it.

RideWatch keeps an explicit foreground monitoring service, releases it when paused, and makes reopening the app a fresh connection request. More recently, I changed absent-radar behavior to use Android's [Bluetooth auto-connect mechanism][background-ble] instead of continually starting direct connection attempts. That can wait for a nearby radar while the app process is alive. It doesn't make the app immune to Android killing the process or the user force-stopping it. The radar-off waiting behavior was checked on my phone; return and overnight power tests are still pending.

Then there is the surprisingly large job of making an alert audible. Media volume, music ducking, speech, urgent interruptions, and output changes all affect what comes out. The current build uses different audio-focus requests for routine and urgent alerts and warns when the actual media volume is below the configured target or muted. Emulator checks exercise the focus and volume behavior, and I've been listening to the alerts on rides. Headphone-switch tests remain to be done.

The automated tests, builds, and screen checks have helped get this far, and riding with it has given me a feel for how useful the spoken alerts can be. Controlled passes with reference measurements are still needed to check the range, speed estimates, and alert timing properly.

I'm happy with it! I can leave my phone in my pocket and hear what's happening behind me while I ride. That's what I wanted when I started this, and having it work on my scooter has been really satisfying.

[background-ble]: https://developer.android.com/develop/connectivity/bluetooth/ble/background
[radar-clip]: https://a.co/d/0e0z4WKo
