---
title: "Fixing the Home Assistant integration for my Dreame L50 Ultra"
date: 2026-11-08
draft: false
slug: "dreame-l50-home-assistant"
tags:
  - home-assistant
  - dreame
  - debugging
  - codex
  - ai-assisted
---

I wanted my Dreame L50 Ultra in Home Assistant, with its settings and room controls available for my own automations. That led to a small maintenance fork, an investigation into what the phone app sends, and a surprisingly consequential missing `@property` decorator.

I directed the work, with Codex doing the investigation, code changes and regression tests. It also helped draft this post. My role was setting the goals and providing the context from my vacuum and Home Assistant setup. The work described here was done in September.

The starting point was [Tasshack's Dreame integration][upstream]. It already does the substantial work: connecting to the vacuum, exposing its settings, handling maps, and providing room-cleaning controls. [My fork][fork] builds on its development branch, starting at `v2.0.0b25`, and maintains the changes on `main`.

## Cleaning up after failed setup

A failed setup attempt can still have created connections, background workers, listeners, and timers. Home Assistant may try again, so those resources need to be cleaned up before another attempt adds more.

[BigKelLearns' cleanup pull request][cleanup-pr] and the associated [upstream issue][cleanup-issue] were useful starting points. The upstream snapshot already cleaned up ordinary update failures. Authentication failures and cancellation needed more attention.

Cancellation was the tricky part. Cancelling an asynchronous task doesn't stop work it has already started in a thread. Disconnect immediately, and that thread can still be creating the very resources you're trying to close.

Codex changed the cleanup to wait for that work before disconnecting. Failed setup and normal unload now use a shared shutdown routine, and pending callbacks are stopped from scheduling more updates afterward. Cleanup errors are logged without hiding the original setup error.

The regression suite includes a hundred failed setup attempts using real worker threads and timers with mocked network responses. It checks that workers stop, sessions close, and the old objects can be collected. Selected tests also failed against the unpatched code in the ways the investigation predicted. That gave us something more concrete than a tidier-looking cleanup function.

## Checking what my L50 actually supports

“L50 Ultra” is the name on the box, but the integration deals with internal model identifiers and firmware capabilities. In the September investigation, account metadata identified mine as `dreame.vacuum.r9493h`, running firmware build 6000.

Codex compared the integration's settings with the configuration and protocol mappings in that model's Dreamehome app plugin. That covered things like wash-water temperature, silent drying, hair compression and obstacle crossing. Most of the settings checked were already supported. The shared plugin serves other models too, so a feature name appearing somewhere in its code wasn't enough to say my vacuum supported it.

There was a general metadata bug to fix as well: a device's key index was checked against the capability table instead of the separate key table it actually indexed. Codex corrected the bounds check and added regression cases.

## The missing decorator

The room-order investigation found a particularly small bug. The integration supports an older room-order format and a newer one, with existing logic to choose between them. But `cleaning_sequence_v2` was missing its `@property` decorator.

The caller expected a boolean. Instead, it got a bound method, which evaluates as true. That forced the newer format even when the selection logic should have chosen the older one.

Adding the decorator restored that logic. Tests cover the modern L50 path and the older fallbacks. This was a reproduced code defect found during the audit; I hadn't observed it failing on the physical vacuum.

## A small maintenance fork

The result is a fork with a few specific defects fixed, building on the much larger integration Tasshack and its contributors made possible. The [validation notes][validation] and [L50 audit][audit] record the checks and the remaining hardware work.

The lifecycle tests use mocked network responses, and the app comparison didn't send cleaning commands or change device properties. Longer-term reconnect behavior and resource use during real cloud outages still need checking. The fixes have regression coverage; that isn't the same as proving every model, firmware version and failure path on hardware.

I'm glad we could turn this investigation into fixes other people can inspect and reuse. If you're working through similar setup failures or model-support questions, the fork and audit notes should give you somewhere to start.

[upstream]: https://github.com/Tasshack/dreame-vacuum
[fork]: https://github.com/nelsonjchen/dreame-vacuum
[cleanup-pr]: https://github.com/Tasshack/dreame-vacuum/pull/1774
[cleanup-issue]: https://github.com/Tasshack/dreame-vacuum/issues/1762
[validation]: https://github.com/nelsonjchen/dreame-vacuum/blob/main/docs/fork-validation.md
[audit]: https://github.com/nelsonjchen/dreame-vacuum/blob/main/docs/l50-plugin-audit.md
