---
title: "Weftan, a backup plan that became a Rust rabbit hole"
date: 2026-09-07T10:34:51-07:00
draft: false
slug: "weftan-rust-rabbit-hole"
tags:
  - rust
  - d2
  - reverse-engineering
  - wasm
  - ai-assisted
  - codex
---

Weftan started as a fun backup plan. If TALA died hard, could I still have the layout engine around in some usable form?

I built Weftan with the assistance of Codex, using the Luna models available at the time. I supplied the ideas and directed the work; Codex did essentially all of the technical investigation and coding described here, including the reverse engineering and Rust implementation. AI also drafted this post.

TALA arranges diagrams for D2. Recovering its behavior and rebuilding it in Rust seemed like an interesting thing to try. That eventually became [Weftan][weftan], and then a larger work-in-progress project called `d2-rs`.

Weftan was faster than the Go version of TALA when that was an external proprietary binary. The slightly annoying part is that TALA built into D2 was faster still. So naturally I started thinking about rewriting more of the surrounding thing too.

## Recovering the behavior

Under my direction, Codex investigated TALA release artifacts: symbols, debugging information, disassembly, and comparisons with the executable. The Go code it reconstructed informed its Rust implementation. This was recovery work, and the provenance matters; I did not independently invent the same layout engine by looking at a few pictures.

Then TALA's source became available in D2 v0.9.0. That changed the situation quite a bit. There was now an upstream source implementation to follow and compare against, instead of relying entirely on recovered behavior.

There is a lot hidden inside “put the boxes somewhere sensible.” Containers, labels, edge routes, and the order in which equally reasonable choices get considered can all affect the final result. Two diagrams can look basically the same while disagreeing on a lot of coordinates.

So the useful comparison was to give both implementations the same measured graph and seed, then compare the actual geometry. When something differed, there was a smaller question to investigate than “why is this picture weird?”

## Matching a corpus

The recorded Weftan baseline reached exact matches on all 120 successful single-seed comparisons in the checked-in corpus. That included 2,964 boxes and 974 routes, along with their corresponding labels and node icons. One large-grid case hit the work limit in both engines and was recorded separately.

I was happy with that. I also want to keep the size of the claim attached to it: those are the cases in that corpus, with that reference version and comparison setup. It does not mean every possible D2 input will behave identically.

Being able to reproduce a mismatch is useful even before it is fixed. It gives the next round of work an actual target. Otherwise it is very easy to keep improving the appearance of one example without knowing whether another example got worse.

The comparison machinery ended up being a useful result of the experiment in its own right.

## The plugin tax

Weftan plugs into D2 as an external layout process. D2 has to pass a graph across that boundary and get the result back. Built-in TALA does not pay that same process and protocol cost.

There are two different comparisons here. Against the old external proprietary Go TALA binary, Weftan was faster in my testing. Against TALA built into D2, the end-to-end measurements went the other way. The baseline changed, and that distinction matters.

Plugin overhead is a plausible explanation for at least some of the gap with built-in TALA, but I have not isolated it well enough to say it accounts for the entire difference. “Rust” on the box does not make the complete workflow faster by itself.

That leaves Weftan in a perfectly reasonable place as a fun experiment. The backup-plan motivation got me started, and I got a recovered engine, comparisons, and a much closer look at the behavior out of it.

It also left me curious about removing that boundary altogether.

## Following upstream in Rust

The next rabbit hole is `d2-rs`: a pure-Rust implementation targeting native execution and WebAssembly, with the layout engines running in the same process. The TALA work from Weftan has moved into that effort.

I am interested in following upstream behavior and exploring parallel execution where it makes sense. Having the surrounding pipeline in Rust should also make it easier to investigate costs without the external plugin boundary sitting in the middle of every layout.

This is still under construction. There are working source-processing and rendering paths, but plenty of compatibility work remains. A working SVG example is a nice milestone; it does not establish that the renderer, all the diagram features, or the other engines are interchangeable with D2.

I also do not have a speedup to announce for the rewrite. That is something to measure as it develops.

For now, [Weftan is public][weftan]. It was a fun backup plan, it taught me things, and it apparently gave me another project to work on.

[weftan]: https://github.com/nelsonjchen/weftan
