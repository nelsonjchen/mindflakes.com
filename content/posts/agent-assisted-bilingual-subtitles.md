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
  - ai-assisted
  - codex
---

I wanted English and Chinese subtitles on screen at the same time for a group watch. Some people read one language faster, some read the other faster. Switching tracks does not help everyone follow along together.

I built the subtitle tools with the assistance of [Codex][codex]. I supplied the ideas and directed the work; Codex did the investigation and implementation, including the merging, timing alignment, and review reports. Codex also drafted this post.

## Matching the tracks

The first script paired English and Chinese cues by their cue numbers, kept the English timestamps, and stacked the text. That worked for one pair of files, but it depended on both tracks numbering their cues the same way. Missing dialogue or extra signage cues can break that assumption.

Another pair needed more work. The timing difference between the tracks changed over the course of the movie, so shifting the whole Chinese track by a fixed number of seconds would not line it up.

I had Codex adapt the script to use the English track as the timing source:

- Estimate the Chinese timing offset in local windows, then smooth those offsets.
- Remap the Chinese cues onto the English timeline.
- Merge Chinese text into the nearest overlapping English cue.
- Keep Chinese-only cues as separate events.
- Write a merged `.srt` and an alignment report.

## Checking the result

The report listed low-confidence merges, Chinese-only cues, English cues without matching Chinese text, and the local offset anchors. That gave me timestamps to check instead of having to inspect the entire subtitle file.

I generated a track, skimmed the report, and spot-checked the questionable sections. When something looked wrong, I asked Codex to adjust the matching or improve the report, then generated it again.

Having Codex write and revise the scripts made this practical for a couple of group watches. I could ask for a change after trying the output with the actual files.

If you want to try it, give your coding agent both subtitle files and specify which track has the correct timing. Ask for the merged output and a report of uncertain matches and unmatched cues. Check those against the video before watching with everyone.

No repo link here. The subtitle files are not mine to redistribute.

It worked for our group watch. We had a good time, and everybody could follow along.

[codex]: https://developers.openai.com/codex/
