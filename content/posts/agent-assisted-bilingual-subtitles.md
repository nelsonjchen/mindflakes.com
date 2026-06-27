---
title: "Agent-assisted bilingual subtitles for group watches"
date: 2026-06-16T15:35:00-07:00
slug: "agent-assisted-bilingual-subtitles"
tags:
  - subtitles
  - python
  - agents
  - localization
  - tooling
---

I made some agent-assisted subtitle tools for group watches.

The short version is that I wanted one subtitle track with English and Chinese at the same time. Some people read one language faster, some read the other faster, and switching subtitle tracks is not a group activity.

No repo link for this one. The subtitle files are not mine to redistribute, and the interesting part is the workflow anyway.

## The group watch problem

The simple version sounds extremely simple:

1. Take an English subtitle file.
2. Take a Chinese subtitle file.
3. Put both lines into one subtitle cue.
4. Watch the thing.

Unfortunately, subtitle files are not just text. They are text, cue numbers, timestamps, line breaks, release-specific offsets, missing cues, extra signage cues, and sometimes a drift that slowly changes across the whole movie.

The first version I tried was basically a mux. If the English and Chinese files had matching cue numbers, use the English timestamp and stack the text together.

That worked well enough for one case. It was also obviously fragile.

## The annoying version

The more interesting case had two subtitle tracks that did not line up with one fixed offset.

If it were just "add 12 seconds to every Chinese cue," this would not be worth writing about. But the offset changed over time. It started around one value and gradually moved toward another.

So the script got more specific:

- parse both subtitle files
- treat the English track as the timing source
- search for the best Chinese offset in local windows
- smooth those offsets
- remap Chinese cues onto the English timeline
- merge Chinese text into the nearest overlapping English cue
- preserve Chinese-only cues as their own events
- write an alignment report for things that looked suspicious

The report was the important part. I did not need the script to pretend everything was perfect. I needed it to tell me where to look.

The useful output was not just the merged `.srt`. It was also a short review list:

- low-confidence merges
- Chinese-only cues
- English cues with no matching Chinese text
- local offset anchors

That made the workflow tolerable. Generate, skim the report, spot-check the weird timestamps, adjust the heuristics if needed, and regenerate.

## Where agents helped

This is exactly the kind of small, specific tool where agents are useful.

I did not need a general subtitle product. I did not need a GUI. I did not need a library that handles every movie ever made. I needed one script for one group watch, and then a slightly better script for a different subtitle mess.

An agent can help make that cheap enough to bother with.

The first pass can be dumb:

```text
same cue number -> same timestamp -> Chinese line + English line
```

Then the next pass can be less dumb:

```text
local timing windows -> estimated offset -> remapped cues -> overlap match -> review report
```

The agent also made it easier to keep the code disposable. I did not have to lovingly design a subtitle framework. I could just ask for the thing I needed, run it, look at the bad spots, and ask for a better report.

If you want to do the same thing, point [Codex][codex], [Claude Code][claude-code], or [Google Antigravity][google-antigravity] at this post and your own subtitle files. The post is basically the spec: one timing source, one translated track, local offset search, merged output, and a review report. That is enough for an agent to build the throwaway script for your specific case.

Anyway, it worked for the group watch. We had a very good time, nobody was lost or left behind!

[claude-code]: https://code.claude.com/docs/en/overview
[codex]: https://developers.openai.com/codex/
[google-antigravity]: https://antigravity.google/
