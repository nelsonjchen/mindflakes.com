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

This sounds like it should be an option in [VoIP.ms][voip-ms]. For all the features VoIP.ms has, this is not one of them. So I made one for cheap.

## The missing filter

[VoIP.ms][voip-ms] has a pretty capable [CallerID Filtering][voipms-callerid-filtering] feature. You can filter on specific caller ID numbers, phone book groups, anonymous callers, calls that do not match the North American number format, STIR/SHAKEN attestation level, and wildcard patterns like area-code-ish blocks.

That is all useful, but these asshole spam callers and scammers do not politely stick to one number. They rotate numbers. Blocking one just means the next call comes from another one. What I wanted to block on was the caller ID name.

VoIP.ms has CNAM support too, but as far as I can tell it is for displaying names, not filtering on them. Their blog post on [stopping spam calls][voipms-spam-blog] talks about filtering numbers, area codes, and anonymous callers, while describing CNAM as a way to identify callers before picking up. The [Caller ID wiki page][voipms-caller-id] describes incoming Caller ID name lookup as an optional per-DID setting that can display a caller name for US and Canadian callers.

There is also a [VoIP.ms Community Forum thread][voipms-forum-thread] where someone asked for exactly this: blocking repeated callers by CNAME/CNAM because the caller used a pile of different numbers. A VoIP.ms staff reply said the feature had been suggested before and that there was no ETA.

So, yes, this is apparently not just me being weird. At least not uniquely weird.

## The Call Hunting trick

VoIP.ms has another feature called [Call Hunting][voipms-call-hunting]. A DID can try multiple destinations in order.

So I made a tool that registers to VoIP.ms as a SIP endpoint and sits first in a [Call Hunting][voipms-call-hunting] group. It checks the incoming caller name and then does one of two things:

- If the caller does not match the block list, it rejects the call with `486 Busy Here`.
- If the caller matches the block list, it answers, plays SIT tones plus a disconnected-style message, and hangs up.

The `486 Busy Here` part is the whole trick. For normal calls, VoIP.ms treats the first destination as busy and keeps going to the next [Call Hunting][voipms-call-hunting] member, which is the real phone path. The blocker gets out of the way.

For blocked calls, the blocker answers the call. Once it answers, the hunt stops. My actual phone never rings.

## Why Twilio Lookup got involved

The first version matched the caller name that VoIP.ms sent in the SIP call.

Then the field I wanted was not the field I got.

For the PCH-style caller I cared about, the VoIP.ms-provided name showed up as a generic city/state-style value. That is not useful enough to block on. Meanwhile, T-Mobile Caller ID showed `PCH` for the same number.

Searching for [`PCH phone number scam`][pch-phone-number-scam-search] gets you Publishers Clearing House, plus warnings about PCH impersonator scams. [PCH has its own scam-report page][pch-scam-report] saying scammers misuse the PCH name to pretend people won prizes and demand money or personal information. The [FTC also has an alert][ftc-pch-impersonators] specifically about PCH impersonators.

I do not know where T-Mobile got that name, and I am not claiming Twilio uses the same source. My understanding is that CNAM is not one magic database anyway. Different carriers and lookup providers can have different data, different caches, and different levels of freshness. Twilio's database may simply be more thorough for this case. VoIP.ms was not giving me the useful label, so I added optional Twilio Lookup.

If configured, the blocker can ask [Twilio Lookup][twilio-lookup] for the caller name and match against that instead. If Twilio does not return a useful name or the lookup fails, the call is allowed through.

The matching itself is intentionally boring:

```sh
BLOCK_CNAME_PATTERNS=pch
```

Those patterns are case-insensitive and match on token boundaries, so `pch` can match `PCH` without accidentally matching some longer unrelated word. There is also a regex option for stranger cases.

## The repo

The project is here:

https://github.com/nelsonjchen/cname_blocker_voip

It is not a PBX. It registers outbound to VoIP.ms, checks caller name information, optionally asks Twilio Lookup, returns `486 Busy Here` for normal calls, and only answers calls it wants to block.

I expect this exact piece of software to be useful mostly for myself, but I do hope people use the code as a starting point. After I posted it in the VoIP.ms thread, [someone asked][voipms-forum-press-1] about using it for a simple "press 1 to continue" style flow instead of dropping the call. I do not need that, and the code is not designed for it, but it is not a ridiculous direction. With an agent like [Codex][openai-codex] pointed at the code and tests, that kind of custom call flow seems very reachable. In a very small and specific way, this program is already acting like a focused PBX.

It is written in Rust, and I deployed it to my TrueNAS box. Resident memory is around 5 MB.

Codex 5.4 Medium helped write a lot of it.

The README has the actual setup steps.

The tests do not need real VoIP.ms credentials either. There is a local fake registrar/integration test path that sends matching and non-matching calls, checks that blocked calls get answered, checks that normal calls get `486 Busy Here`, and verifies RTP audio makes it through.

Anyway, it works for the annoying thing I wanted to stop.

I still think VoIP.ms should offer caller-name filtering directly. Until then, this does the job.

[cname-blocker-repo]: https://github.com/nelsonjchen/cname_blocker_voip
[ftc-pch-impersonators]: https://consumer.ftc.gov/consumer-alerts/2023/12/hang-pch-impersonators
[openai-codex]: https://openai.com/codex/
[pch-phone-number-scam-search]: https://www.google.com/search?q=PCH+phone+number+scam
[pch-scam-report]: https://scamreport.pch.com/
[twilio-lookup]: https://www.twilio.com/docs/lookup/v2-api
[voip-ms]: https://voip.ms/
[voipms-call-hunting]: https://wiki.voip.ms/article/Call_Hunting
[voipms-caller-id]: https://wiki.voip.ms/article/Caller_ID
[voipms-callerid-filtering]: https://wiki.voip.ms/article/CallerID_Filtering
[voipms-forum-press-1]: https://community.voip.ms/t/blocking-based-on-caller-id/2883/10
[voipms-forum-thread]: https://community.voip.ms/t/blocking-based-on-caller-id/2883
[voipms-spam-blog]: https://voip.ms/blog/how-to-stop-spam-calls-voip/
