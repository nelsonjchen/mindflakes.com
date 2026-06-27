---
title: "Plainize Clip"
date: 2026-06-14T23:05:00-07:00
slug: "plainize-clip"
tags:
  - macos
  - swift
  - clipboard
  - appkit
  - reverse-engineering
  - codex
---

I made [Plainize Clip][plainize-clip-repo], a tiny macOS app that cleans the current clipboard and quits.

The short version is that I still wanted the old [Plain Clip][plain-clip] shape: copy weird text, launch the app, paste something boring.

Not a clipboard manager. Not a menu bar app. Not a background process sitting around in RAM forever. I launch it through Spotlight, it rewrites the pasteboard as plain text, and then it exits.

## The annoying clipboard problem

Copying text on macOS is often not just copying text.

Sometimes it brings formatting. Sometimes it brings weird invisible characters. Sometimes it brings smart quotes, hard-wrapped lines, non-breaking spaces, tabs, and whatever else got dragged along from a PDF, web page, chat app, or ticketing system.

Usually I do not want a whole clipboard workflow. I just want the pasteboard to stop being annoying.

So Plainize Clip does the small version:

1. Read the general pasteboard.
2. If there is text, clean it.
3. Write back plain text only.
4. Quit.

That is the whole normal launch path.

## Launch, clean, quit

The important behavior is that Plainize Clip is faceless.

There is no Dock icon to manage after the cleanup. There is no persistent process. There is no history database. If the current pasteboard has text, it cleans it with the saved preferences and replaces the pasteboard contents with text.

The prod was Apple's [Rosetta][apple-rosetta] clock. Apple says Rosetta will remain available as a general-purpose Intel app translation tool through macOS 27, and then becomes limited starting with macOS 28. So an Intel-only utility like the old Plain Clip had a stronger "you should probably stop depending on this forever" vibe.

Choosing Swift/AppKit/SwiftUI was not a deep architectural statement. I just wanted to follow Apple's current recommendations for a small Mac app.

The [GitHub releases][plainize-clip-releases] are unsigned, so macOS does the usual "unknown developer" dance. Apple's support page documents the [Open Anyway][apple-open-unknown] flow. This is annoying, but paying Apple for a Developer ID certificate for a small personal clipboard cleaner is also annoying.

## Reverse-engineering because Codex made it cheap

I also ended up reverse-engineering the old app more than I normally would have.

[Codex][codex] let me reverse-engineer stuff I otherwise would not have lifted a finger for. The local `plain-clip-re` workspace became the notebook for it: static inventory, Ghidra headless exports, behavior notes, and then a recovered map of what the old app actually did.

The useful bits were not mysterious. They were just tedious:

- normal launch cleans the pasteboard and quits
- holding Shift at launch opens preferences
- the command-line wrapper has switches for the same cleanup options
- the cleaner reads string content from the general pasteboard
- after cleaning, it replaces the pasteboard with string-only content
- the pipeline handles line endings, whitespace, hard wraps, blank lines, smart quotes, tabs, Unicode normalization, and ASCII conversion

That was enough to rebuild the behavior intentionally instead of just making "a clipboard cleaner, probably."

Plainize Clip is still a separate implementation. I did not reuse the old app's code. The reverse-engineering work was mostly there to answer boring compatibility questions without relying on memory.

## The cleanup switches

Plainize Clip has preferences because text cleanup is personal enough to need switches.

The defaults do the stuff I usually want: trim whitespace, remove empty-line noise, join hard-wrapped prose, replace tabs with spaces, collapse repeated spaces, remove invisible control characters, and turn smart quotes and typographic dashes into simpler punctuation.

There are also options for Unicode normalization and ASCII conversion. The ASCII path tries to romanize non-Latin scripts before filtering, so it does not turn a clipboard full of CJK, Korean, Arabic, Hebrew, or Cyrillic text into an empty string. It is best-effort, not magic.

Preferences open by holding Shift while launching, or from Terminal:

```sh
open -a "/Applications/Plainize Clip.app" --args --preferences
```

Save the settings and it runs one cleanup pass immediately. Cancel does not.

## The Plain Clip context

Plainize Clip borrows the spirit of [Plain Clip][plain-clip] by [Carsten Blüm][carsten-bluem]: stay invisible, do one job, quit.

It is independent from Plain Clip and is not affiliated with Blüm.

The context is that I liked Plain Clip, but the old [archived 2.5.2 build][plain-clip-archive] I had found was Intel-only, the source release situation was unclear, and Blüm's current site no longer listed those legacy Mac apps by default. His site says older macOS apps moved out of the normal public listing, and that interested users can contact him for specific versions.

Blüm later clarified to me that Plain Clip is still in daily use by him and that he has resumed work on it, including an Apple Silicon build. As far as I know, that newer build is still an on-request thing rather than something posted publicly.

My approach was different: Plainize Clip is open on GitHub from the start. It remains my separate modern build of the shape I wanted.

## The repo

The project is here:

https://github.com/nelsonjchen/plainize-clip

The releases are here:

https://github.com/nelsonjchen/plainize-clip/releases

The README has the download notes, unsigned-app warning, preferences, CLI wrapper, build command, and tests.

[apple-open-unknown]: https://support.apple.com/guide/mac-help/open-a-mac-app-from-an-unknown-developer-mh40616/mac
[apple-rosetta]: https://support.apple.com/en-us/102527
[carsten-bluem]: https://www.bluem.net/
[codex]: https://developers.openai.com/codex/
[plain-clip]: https://www.macworld.com/article/195102/plainclip2.html
[plain-clip-archive]: https://web.archive.org/web/20250331212359/https://www.bluem.net/files/plain-clip-2.5.2.zip
[plainize-clip-releases]: https://github.com/nelsonjchen/plainize-clip/releases
[plainize-clip-repo]: https://github.com/nelsonjchen/plainize-clip
