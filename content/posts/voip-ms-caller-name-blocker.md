---
title: "Blocking spammy caller names with VoIP.ms Call Hunting"
date: 2026-05-20T19:30:00-07:00
slug: "voip-ms-caller-name-blocker"
tags:
  - voip
  - sip
  - rust
  - voip-ms
  - telephony
---

I made a small [Rust VoIP.ms CNAME blocker][cname-blocker-repo].

The short version is that I wanted to block a caller by name instead of by phone number.

This sounds like it should be a normal checkbox somewhere. It is not.

## The missing filter

[VoIP.ms][voip-ms] has a pretty capable [CallerID Filtering][voipms-callerid-filtering] feature. You can filter on specific caller ID numbers, phone book groups, anonymous callers, calls that do not match the North American number format, STIR/SHAKEN attestation level, and wildcard patterns like area-code-ish blocks.

That is all useful. Unfortunately, it is also very number-shaped.

There are callers where the number is not the stable part. The useful part is the caller name, or CNAM, or CNAME, or whatever exact telephony word-shaped object is coming through today.

VoIP.ms even has a blog post on [stopping spam calls][voipms-spam-blog] that frames call filtering around numbers, area codes, and anonymous caller status. In the same post, CNAM is described as a way to identify callers before picking up. The [Caller ID wiki page][voipms-caller-id] also describes incoming Caller ID name lookup as an optional per-DID setting that can display a caller name for US and Canadian callers.

That is helpful! But identifying a caller and filtering a caller are not the same thing.

There is also a [VoIP.ms Community Forum thread][voipms-forum-thread] where someone asked for exactly this: blocking repeated callers by CNAME/CNAM because the caller used a pile of different numbers. A VoIP.ms staff reply said the feature had been suggested before and that there was no ETA.

So, yes, this is apparently not just me being weird. At least not uniquely weird.

## The Call Hunting trick

VoIP.ms has another feature called Call Hunting. The normal idea is that a DID can try multiple destinations in order. If the first one does not take the call, the next one gets a shot.

That is enough rope to build something useful.

So I made a tool that registers to VoIP.ms as a normal SIP endpoint and sits first in a Call Hunting group. It looks at the incoming caller name and then does one of two things:

- If the caller does not match the block list, it rejects the call with `486 Busy Here`.
- If the caller matches the block list, it answers, plays SIT tones plus a disconnected-style message, and hangs up.

The `486 Busy Here` part is the whole trick. For normal calls, VoIP.ms treats the first destination as busy and keeps going to the next Call Hunting member, which is the real phone path. The blocker gets out of the way.

For blocked calls, the blocker answers the call. Once it answers, the hunt stops. My actual phone never rings.

It's a bit of a hack, but it is a nice kind of hack. No inbound port forwarding. No PBX to babysit. Just a small SIP endpoint with one job.

## Why Twilio Lookup got involved

The original simple version matched the caller name that VoIP.ms delivered in the SIP call.

Then reality did the thing where the one field you want is not the field you get.

For the PCH-style caller I cared about, the VoIP.ms-provided name showed up as a generic city/state-style value. That is not useful enough to block on. Meanwhile, T-Mobile Caller ID showed `PCH` for the same number.

That does not prove T-Mobile and Twilio are using the same database. CNAM is messy enough that I do not want to pretend certainty here. But it did suggest there was a better name source out there than the one I was getting through VoIP.ms.

So I added optional [Twilio Lookup][twilio-lookup] support. If configured, the blocker can ask Twilio for the caller name and match against that instead. If Twilio does not return a useful name or the lookup fails, the call is allowed through.

The matching itself is intentionally boring:

```sh
BLOCK_CNAME_PATTERNS=pch
```

Those patterns are case-insensitive and match on token boundaries, so `pch` can match `PCH` without accidentally matching some longer unrelated word. There is also a regex option for stranger cases.

## The repo

The project is here:

https://github.com/nelsonjchen/cname_blocker_voip

It is not trying to be a complete PBX. It is a narrow little tool:

- register outbound to VoIP.ms
- inspect caller name information
- optionally use Twilio Lookup
- let normal calls continue through Call Hunting
- answer only blocked-name calls
- play a local disconnected message

The README has the actual setup steps.

The tests do not need real VoIP.ms credentials either. There is a local fake registrar/integration test path that sends matching and non-matching calls, checks that blocked calls get answered, checks that normal calls get `486 Busy Here`, and verifies RTP audio makes it through. That made this feel a lot less like a pile of SIP hope.

Anyway, it works for the annoying thing I wanted to stop.

I still think VoIP.ms should offer caller-name filtering directly. Until then, this tiny weird SIP detour is doing the job.

[cname-blocker-repo]: https://github.com/nelsonjchen/cname_blocker_voip
[twilio-lookup]: https://www.twilio.com/docs/lookup/v2-api
[voip-ms]: https://voip.ms/
[voipms-caller-id]: https://wiki.voip.ms/article/Caller_ID
[voipms-callerid-filtering]: https://wiki.voip.ms/article/CallerID_Filtering
[voipms-forum-thread]: https://community.voip.ms/t/blocking-based-on-caller-id/2883
[voipms-spam-blog]: https://voip.ms/blog/how-to-stop-spam-calls-voip/
