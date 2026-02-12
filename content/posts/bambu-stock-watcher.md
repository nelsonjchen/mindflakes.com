---
title: "Bambu Lab Store Filament Tracker"
date: 2026-02-11T20:10:00-08:00
tags:
  - bambu
  - filament
  - duckdb
  - typescript
  - cloud run
  - cloudflare pages
image: "/images/bbltracker.com.png"
---

I've been running a [Bambu Lab](https://bambulab.com/) Store filament tracker at [bbltracker.com](https://bbltracker.com/).

![bbltracker dashboard](/images/bbltracker.com.png)

I recently got into [3D printing](https://en.wikipedia.org/wiki/3D_printing). A lot of what I print is practical: last-millimeter adapters, things like [robot vacuum](https://en.wikipedia.org/wiki/Robotic_vacuum_cleaner) ramps around the house, and other tiny fixes that make daily life less annoying.

[Bambu Lab](https://bambulab.com/) released the [P2S](https://bambulab.com/en-us/p2s), their flagship midrange printer, and I picked one up after hearing how polished their ecosystem is. People call them the Apple of 3D printers, and I can see why. The hardware is solid, but the software and ecosystem are really what make it shine.

There is also a bit of a format war around [filament](https://en.wikipedia.org/wiki/Fused_filament_fabrication). Bambu sells filament with [RFID](https://en.wikipedia.org/wiki/Radio-frequency_identification) tags that are not easy for third parties to mimic. The RFID flow makes loading filament easy and automatically applies Bambu-tuned profiles. Some people feel those profiles run too fast, but for my functional prints, they have worked great.

## Where the frustration started

I like having color options, and color can be functional too. But stock has been rough for a while, even after the holiday season.

Then there is the constant bulk-sale mechanic on the [Bambu Lab Store](https://us.store.bambulab.com/?skr=yes): buy 4+, get a strong discount, buy more, save more. In practice, that falls apart when the combinations you want are never all in stock at the same time.

At the time, the store UI also made this harder than it needed to be. Out-of-stock options were not clearly crossed out, so building a cart felt like guesswork.

That made me angry, but in a constructive way. So I built a tracker.

## What the tracker does

At a high level, the tracker monitors store inventory over time, keeps historical snapshots, and renders a dashboard so I can quickly answer:

- What is actually in stock right now?
- What appears stable versus constantly draining?
- What was the last major restock spike?
- Are there ETA hints for restocks?
- Is this likely to go out of stock soon?

I started with only the [US store](https://us.store.bambulab.com/?skr=yes), then expanded to [EU](https://eu.store.bambulab.com/?skr=yes), [CA](https://ca.store.bambulab.com/?skr=yes), [UK](https://uk.store.bambulab.com/?skr=yes), [AU](https://au.store.bambulab.com/?skr=yes), and [Asia](https://asia.store.bambulab.com/?skr=yes) coverage.

I also reused a stock-quantity verification trick I had learned while documenting [openpilot](https://github.com/commaai/openpilot)'s Toyota security key journey.

## Building it fast (and keeping it moving)

I used a lot of [Google Antigravity](https://antigravity.google/) while building this. [Web scraping](https://en.wikipedia.org/wiki/Web_scraping) is tedious work, and [large language models](https://en.wikipedia.org/wiki/Large_language_model) helped a lot with iteration speed and the constant tweaks.

The codebase is not pretty in every corner, but LLMs have made it very workable to maintain and improve continuously.

Later, I added depletion-rate estimation so the tracker can make a rough guess about when something might go out of stock.

## Things that happened after launch

After I released it and posted it on [/r/BambuLab](https://www.reddit.com/r/BambuLab/), a bunch of interesting things happened:

- Bambu nerfed the original stock-count hole the tracker used.
- For a while, counts were capped at 200 in the US view, which reduced visibility.
- Later, that cap moved to 400 in the [US store](https://us.store.bambulab.com/?skr=yes). Maybe my tracker's utility was actually a factor in that decision? I do see traffic from China, where there is no Bambu Lab Store of the same codebase as far as I can see.
- Bambu improved their UI and added clearer out-of-stock cross-outs.
- I added more quality-of-life indicators, including "stable stock," ETA context, and restock-spike visibility.
- I added a [GoFundMe](https://gofund.me/b1be0586b) and a nice patron funded a custom domain and hosting for the tracker.

## Backend at a glance

Keeping this online reliably has mostly been a deployment and operations problem:

- [Google Cloud Run](https://cloud.google.com/run) is heavily used for scheduled jobs, scaling, and reliability.
- [Cloudflare Pages](https://pages.cloudflare.com/) hosts the output and leaves room for future expansion.

If you print a lot and buy filament in batches, this has been much better than manually refreshing product pages and hoping for the best.
