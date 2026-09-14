---
title: "Could the Crazy Taxi Arcade TAS use one fewer passenger?"
date: 2026-08-10T10:34:51-07:00
draft: false
slug: "crazy-taxi-one-fewer-passenger"
tags:
  - crazy-taxi
  - reverse-engineering
  - games
  - tas
---

I got curious about whether a Crazy Taxi tool-assisted speedrun could be slightly faster with one fewer passenger.

I do not really have TAS skills. This was a “could that work?” rabbit hole that led to looking at passenger tables, destination selection, and the game clock. I have not made a faster run.

The tips are where the real TAS money is. Chaining Crazy Throughs and drifts can pile up money while carrying a single passenger. [SJ's run][sj-route] aims for the Crazy License, reaches $20,000 with four passengers, and uses the long Police Station trip for a huge combo chain. The run ends when the clock reaches zero. So the idea is to earn enough money from tips with fewer fares, then have less clock left to burn off.

The specific idea starts after the first drop-off at **Cable Car Stop TOP**, the plaza at the top of the hill. In [SJ's four-passenger route][sj-route], the next pickup there goes to **West Side Beach**, followed by a passenger going to **Popcorn Mania**, and then one going to the **Police Station**.

My idea was to leave the plaza and grab the side-street passenger going to **FILA** instead of taking the nearby passenger to the beach. FILA puts me near the next Police Station pickup, so I could go from that drop-off to the final passenger without doing the beach-to-Popcorn Mania fare in between. FILA appears as “Shoe Rack” in the Android data I used for the investigation.

That is why three passengers seemed promising: skip a whole fare and its extra pickup and drop-off, while keeping the long Police Station trip for earning tips. The side-street detour and the changed route could affect traffic and combo opportunities, so I would still need to show that three passengers can earn enough. Fewer stops only help if the money works out too.

## The clock is part of the problem

Picking up a passenger adds time to the Arcade clock. A successful delivery can add more. So changing the passenger route also changes how much time the game hands back to you.

The symbol-rich Android release was useful for investigating this. Its code shows a distance-based time allowance at pickup, with a multiplier that changes as play goes on, plus a delivery-rank bonus at drop-off.

In the calculation in my notes, a proposed three-passenger route adds about 12.56 fewer seconds than the four-passenger reference route. That comparison assumes the early-game multiplier and the best delivery judgement for each passenger.

That is less added game time, which made the idea interesting enough to investigate further. It is not a measured 12.56-second improvement in a TAS. Driving, passenger animations, and when the clock actually advances still have to be accounted for.

## Which destination do you get?

The Android investigation also showed that passenger destination selection follows a counter. It cycles through three destination slots, with each slot lasting 600 passenger-manager updates, roughly ten seconds at 60 updates per second.

The choice is latched when the passenger event activates. Waiting until boarding finishes to reason about it would put the timing at the wrong point.

That makes route planning more interesting. Reaching a passenger at a different time can select a different destination, which changes the trip and the clock allowance. Picking three convenient dots on a map is not enough.

To look at this spatially, I forked the [noclip.website](https://noclip.website/) viewer and loaded the GameCube Arcade city map with the extracted pedestrian pickup locations overlaid on it. Credit to noclip.website for the viewer; my additions turned it into a research map for this passenger-routing rabbit hole. It is much easier to reason about a side-street pickup when I can actually see where it sits in the city.

The extracted passenger records match the Android reference records, which is useful evidence for understanding the data. It does not prove that every timing behavior carries over to the GameCube executable.

## Maybe later

The next step would be to verify the relevant behavior in the GameCube version and try the route in an actual TAS workflow. There is still a gap between a promising calculation and inputs that produce a faster run.

For now this is a curiosity with some code and notes behind it. I need to sit down with it when things are a bit less crazy and I have time to learn the TAS side properly. I might revisit it then. One fewer passenger sounded like a small question. Apparently it came with homework.

[sj-route]: https://tasvideos.org/9374S
