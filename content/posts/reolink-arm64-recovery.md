---
title: "Recovering readable C++ from Reolink's Apple Silicon SDK"
date: 2026-10-03
draft: false
slug: "reolink-arm64-recovery"
tags:
  - reverse-engineering
  - reolink
  - cpp
  - arm64
  - codex
  - ai-assisted
---

I wanted to have a go at making my own ARM64 port of Reolink's macOS app. They had not released one yet, and getting there first would have been fun to show off. Spoiler alert: they beat me. Oh well.

I'm doing this with the assistance of [Codex](https://openai.com/codex/). I supplied the goals and directed the work; Codex did the reverse engineering, implementation, and comparison tests described here.

I was also curious why an Electron app was still waiting for an ARM64 version. It turned out there was much more native code involved than that description suggested. The interface uses Electron, but communication with Reolink's cameras and NVRs goes through a native C++ SDK, with its own device state transitions, encryption, media handling, configuration serializers and callback machinery. Porting that meant understanding considerably more than the interface.

Reolink beat me to releasing an ARM64 version. The work has continued, now using the official Apple Silicon binaries as a reference for reconstructing readable C++.

I still want a thorough understanding of what Reolink's hardware and SDK can do, and a way to investigate some of the crashes in the official macOS app. Understanding the implementation might make those crashes easier to diagnose and, hopefully, fix.

A readable reconstruction could also help with Home Assistant integrations and custom apps. That connects to my [Home Assistant camera-announcement work]({{< relref "reolink-laundry-announcements.md" >}}) and the [Guard Dog Android TV app and its Swift macOS sister app, Sentry]({{< relref "guard-dog-camera-wall.md" >}}). Understanding the native SDK could help explain device behavior and expose capabilities useful to those projects, even while the replacement is unfinished.

Recovering one small method can expose enough of the surrounding contract to make the next one approachable. One of the recurring problems is deciding what "recovered" actually means. A function can return the expected number while getting ownership, callbacks or object layout wrong in ways that only show up later.

C++ symbols help more than I expected. When the binary exposes a mangled name, it provides clues about the class, namespace, arguments and qualifiers. Codex reconstructs declarations that make the compiler naturally emit the same name. A convenient exported alias can keep a boundary callable, but it leaves unanswered questions about the actual declaration behind it.

The tests need to follow those questions. For a configuration callback, I care about the exact XML bytes and response fields. For encryption, the output buffer and error handling matter alongside the return value. For an object destructor, I need to know which resource is released, which state is cleared and which object owns the final deletion.

One recent example was the encryptor's lifecycle. Codex recovered the RSA resource ownership and separate destructor variants to replace a minimal destructor with ordinary C++. Constructors had their own initialization and layout checks. Those are small-looking functions with consequences beyond whether the immediate test call crashes.

Device opening is more involved. Codex reconstructed and compared portions of the policy and state-machine chain, including transitions through opening and login states. Some checks use fixture-owned callbacks or inert transport boundaries to exercise ordering and return mapping repeatedly. That proves the covered boundary contract; the real network implementation and a successful live device session need their own evidence.

Codex also compares compiler output with the original ARM64 instructions. That catches a different set of mistakes from behavior tests: missing branches, argument-order errors, unexpected memory writes or an incorrect calling convention. Compiler differences can remain even when a readable implementation has useful behavior and ABI evidence. The comparison notes record that drift. I want the implementation to stay readable, even when the remaining mismatch lowers its score.

By early October, the work had moved further into natural callback-operation classes and configuration dispatch, with focused runtime, symbol, object-layout and generated-code checks. Splitting implementations into source files and testing their default build path has been useful too. A standalone test can accidentally supply a helper that the actual replacement doesn't own.

The replacement SDK is still unfinished. Partial builds have unresolved symbols, mocked boundaries remain, and successful local comparisons don't establish a complete working transport or desktop client. I keep those categories separate when counting progress, because a tested wrapper around an unknown implementation is still an unknown implementation.

The useful output so far is a growing body of understandable C++, plus checks that explain why I believe particular pieces match. There's plenty left to recover. At least the next investigation can start from an explicit contract, with the remaining unknowns written down.
