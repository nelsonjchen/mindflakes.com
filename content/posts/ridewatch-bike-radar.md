---
title: "Building an Android app for spoken radar alerts on my scooter"
date: 2026-10-03
lastmod: 2026-11-11
draft: false
slug: "ridewatch-bike-radar"
tags:
  - android
  - scooter
  - bluetooth
  - codex
---

When I ride my scooter, I use a [Garmin Varia RVR315](https://www.garmin.com/en-US/p/669024/) bike radar to help keep track of vehicles approaching from behind. The radar clips onto the back of my CamelBak with an [EFEALL strap mount][radar-clip], and my phone stays in my pocket. I'm not a fan of mounting the phone on the scooter, so I wanted spoken information about vehicles approaching from behind without having to look at a screen.

The radar sends its detections over Bluetooth. I also have the recurring problem of Garmin's Android app stopping in the background and me forgetting to open it again. I wanted clearer connection behavior as well as the spoken alerts.

The clip is sold as a saddle-bag strap mount with a Garmin quarter-turn fitting, including support for the RVR315. It can rotate and tilt and comes with a safety tether. I'm using it on my CamelBak instead of a bike's saddle bag.

That turned into [RideWatch][ridewatch-source], a standalone Android app I built with Codex doing the research and implementation under my direction. The source is now public. It's still a prototype, but I've been using it on scooter rides.

It's neat how much I can follow by listening. I can tell when a car behind me is slowing down to pull over or park, or when one is approaching very quickly. Those changing alerts give me a sense of what's happening behind me while my phone stays in my pocket.

The car count is useful too. If I'm waiting to make a U-turn and it announces two cars behind me, I know I'm waiting for two cars to pass. After the first one goes by, there's still another one to account for. My phone can stay in my pocket through all of this.

None of this replaces looking, listening, and taking the usual precautions. Hearing two cars doesn't mean there can't be a third, and silence isn't an all-clear. It's an extra source of awareness while riding, and I've found it really useful in that role.

The first job was finding out what the radar actually sends. Public work on other Garmin radars provided leads, but a newer model's packet layout isn't automatically the RVR315's layout. Codex inspected the Android Varia app to trace its Bluetooth subscriptions and legacy decoder, then wrote a separate implementation. Garmin's code and sound recordings aren't bundled into RideWatch.

The app now has a graphical lane showing detected vehicles, threat changes, synthesized chimes, and optional speech. The speech is deliberately brief: numbers rather than a full sentence repeated every second. For multiple vehicles, it can also announce changes in the count. An old queued announcement needs to expire when the underlying situation changes; speaking stale information clearly is still speaking stale information.

## A simulated ride

Here's a short example with the app’s actual synthesized tones and numeric-repeat speech enabled. The detections and speed estimates are made up, using the wording from RideWatch's speech policy. Numbers are estimates in mph. The speech uses a computer voice; the Android voice on your phone will sound different. Tones play before speech, as they do in the app.

<audio controls preload="none" aria-label="Eighteen-second simulated RideWatch tones and voice alerts">
  <source src="/audio/ridewatch-voice-demo.mp3" type="audio/mpeg">
  <a href="/audio/ridewatch-voice-demo.mp3">Listen to the simulated voice alerts</a>.
</audio>

| Time | Simulated situation | Sound |
| --- | --- | --- |
| 0 seconds | Radar connects with no targets | Connected tone |
| 2 seconds | One car detected | Approach tone, then “22” |
| 4 seconds | Its speed estimate drops | “16” |
| 6 seconds | A second car appears and the threat level rises | Urgent tone, then “2 cars. 24” |
| 8 seconds | Still two cars; the number updates | “23” |
| 10 seconds | One car remains detected | “1 car. 20” |
| 12 seconds | No targets remain detected | Empty-stream tone; no speech |
| 14 seconds | Radar disconnects; last known battery was 70% | Lost-connection tone, then “Disconnected. Last battery 70 percent” |

The count is announced when it changes within a group; the repeated numbers stay short. A lone car normally gets just the number. No detected targets means no speech in this mode, and silence still isn't an all-clear.

## Building and testing it

The speed part remains experimental. Closing speed relative to me on the scooter and a car's speed along the road are different things. RideWatch can combine the decoded value with a recent phone estimate of rider speed, but the interpretation needs a controlled vehicle pass and reference measurements. Recovering arithmetic from an app isn't the same as validating that estimate on the road.

Getting the first physical connection working produced a wonderfully mundane bug. Android's companion-device API returned a Bluetooth address with lowercase hexadecimal letters. The Bluetooth parser used for the next step expected uppercase. The app failed before it even tried to connect to the radar.

Normalizing saved addresses, including ones from earlier installs, fixed that. My Pixel then showed the radar's battery reading and received a live stream with no targets. That established a real connection and decoded notifications. It didn't establish vehicle range, speed accuracy, or alert timing.

The fix for keeping RideWatch running in the background is a persistent notification. While monitoring is enabled, the app keeps an Android foreground service running with that notification, even after I leave the app and haven't opened it for a while. The version of Garmin's app I have doesn't keep a notification going like this. Having RideWatch stay active between uses addresses one of the main reasons I wanted to build it.

RideWatch keeps an explicit foreground monitoring service, releases it when paused, and makes reopening the app a fresh connection request. More recently, I changed absent-radar behavior to use Android's [Bluetooth auto-connect mechanism][background-ble] instead of continually starting direct connection attempts. That can wait for a nearby radar while the app process is alive. It doesn't make the app immune to Android killing the process or the user force-stopping it. The radar-off waiting behavior was checked on my phone; return and overnight power tests are still pending.

Then there is the surprisingly large job of making an alert audible. Media volume, music ducking, speech, urgent interruptions, and output changes all affect what comes out. The current build uses different audio-focus requests for routine and urgent alerts and warns when the actual media volume is below the configured target or muted. Emulator checks exercise the focus and volume behavior, and I've been listening to the alerts on rides. Headphone-switch tests remain to be done.

The automated tests, builds, and screen checks have helped get this far, and riding with it has given me a feel for how useful the spoken alerts can be. Controlled passes with reference measurements are still needed to check the range, speed estimates, and alert timing properly.

One known issue: the battery reading seems stuck at 83%, including in Garmin's original app. I don't know why yet, so I wouldn't rely on that percentage.

I'm happy with it! I can leave my phone in my pocket and hear what's happening behind me while I ride. That's what I wanted when I started this, and having it work on my scooter has been really satisfying.

[background-ble]: https://developer.android.com/develop/connectivity/bluetooth/ble/background
[radar-clip]: https://a.co/d/0e0z4WKo
[ridewatch-source]: https://github.com/nelsonjchen/varia-re
