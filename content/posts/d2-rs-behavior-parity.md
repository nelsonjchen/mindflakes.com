---
title: "Introducing d2-rs: a Rust port of D2 for the CLI and WebAssembly"
date: 2026-11-07
lastmod: 2026-11-08
draft: false
slug: "d2-rs-behavior-parity"
tags:
  - rust
  - d2
  - compatibility
  - codex
  - claude
  - ai-assisted
---

I've released [d2-rs][repo], a Rust port of the [D2][d2] diagramming toolchain. It includes the compiler, Dagre, ELK and TALA layout engines, SVG rendering, PNG/PDF/PPTX/GIF exports, and a command-line app. It also builds as a WebAssembly package for Node and browsers, which was one of my original reasons for wanting the port.

I directed the project over about two months, from September to early November. [Codex](https://openai.com/codex/) did most of the investigation and implementation for the first six weeks. Then [Claude](https://claude.com/) supervised Codex runs, reviewed changes and chose the next work, as well as contributing implementation. The port follows D2's source and uses its test suites to check the results.

The current release targets Go D2 master at `6a7b48378`, from October 11. All 944 boards in upstream's end-to-end golden suite match: 933 SVGs and 11 expected errors, across all three layout engines. That comparison normalizes the XML declaration, version stamp and trailing newline; the rest of the SVG must match byte for byte.

You can install the command-line app from [crates.io][crate]:

```sh
cargo install d2-rs
d2-rs input.d2 output.svg
```

Or install the [WebAssembly package][npm], which follows D2.js's `compile` / `render` API shape:

```sh
npm install @d2-rs/wasm
```

It's an independent, unofficial project, licensed under MPL-2.0 like D2. All three layout engines run in process.

{{< figure src="/images/d2-rs-workflow.png" alt="D2 source goes through Go D2 and d2-rs, producing diagrams whose output, time and memory are compared. The d2-rs output is labelled This diagram." caption="d2-rs drawing its own comparison workflow, rendered with ELK. A little meta." link="/images/d2-rs-workflow.png" width="520" >}}

The [D2 source for this diagram](/images/d2-rs-workflow.d2) is available too.

## We ended up porting a lot of Go

This part still amazes me: getting D2's output to match meant reimplementing a substantial amount of the Go code underneath it in Rust. A perfectly good Rust library can produce a different compressed stream, format a float differently, or choose a different sort order. Those differences matter when the target is the same bytes.

So the agents ported the Go behavior D2 depends on: float parsing and formatting, sorting, random-number generation, compression, image encoding, ZIP output, and more. Even the arithmetic needed attention. Go can fuse a multiply and add on arm64 into a single rounding step, while the x86-64 implementation uses separate operations. The Rust port had to follow the corresponding architecture to get the same result.

That reached into D2's dependencies too: Markdown parsing, code highlighting, LaTeX, text shaping, sketch rendering and PDF generation. The [project's inventory][go-ports] gives a fuller picture. Go's deflate and zlib implementation is already available as the standalone `d2-rs-goflate` crate; many of the other ports are modules within the crates that use them.

That's pretty hardcore. I set out wanting D2 in Rust and WebAssembly, and the work reached all the way down to compression decisions and floating-point rounding. The shipped implementation runs in Rust; Go is used as the reference for testing it.

## Correct, but stupidly slow

Getting the output to match was a lot of work, but I was dismayed that GPT considered the resulting performance acceptable. Codex had produced a version that followed Go's behavior, but it was stupidly slow. I wanted a performant Rust version that improved on the original. Getting the right answer was necessary, but performance was a big part of why I wanted this at all. I had to push explicitly for that part of the goal.

An October 26 benchmark on a clean Linux arm64 host took 377 ms to lay out `grid_large_checkered` with Dagre, against Go's 44 ms: about 8.6 times as long. Across the twelve TALA cases in that run, the total runtime was about 3.9 times Go's. Some tiny diagrams were already faster, which made a single headline median misleading. The larger diagrams were where the port was falling behind.

We were going to run a lot of tests against this thing. A slowdown like that can turn an hour-long test run into three hours, and that cost comes back every time we check a change. It would make the whole project slower to work on.

The trouble was that GPT tried to emulate too much of Go's internal machinery in Rust. Go's shared, mutable graph objects became layers of reference counting and locking. Elsewhere, the port copied collections to satisfy Rust's ownership rules where Go shared a slice or pointer. Inside a layout engine that tries thousands of candidate placements, those costs kept adding up: extra allocations, copies and bookkeeping on every attempt.

Matching Go's output mattered, but that didn't require carrying all that overhead into the Rust implementation. A lot of the later speed work was about preserving the behavior while changing how the data was stored, shared and reused.

## Claude directing Codex

Claude had a much better sense of what mattered here. It recognized that a port this slow would be painful to use and test, even if the output matched. The later work focused on measuring where the time and memory went, fixing the implementation, and keeping the output matching Go throughout.

I also wanted to take advantage of the numerous Codex usage resets during this period. My idea was to have the model with better judgment direct models that were smart and capable at coding, but hadn't shown the same sense of priorities on this project. Claude could choose the next work and review the results while Codex did much of the implementation.

That work reached into graph storage, routing queues, repeated compiler work and memory allocation. Preserving Go's behavior still mattered, but the way the Rust code achieved it needed substantial changes.

As the port got faster, the tests got faster too. More changes could be tried and checked in the same amount of time, so the project started improving much more rapidly. The performance work made the rest of the work easier to do.

## Where it ended up

The initial benchmark covered twelve diagrams through three engines. That left too much unmeasured, so the work expanded to the whole upstream corpus and PNG, PDF, PPTX and GIF exports as well as SVG.

On the recorded Linux arm64 corpus sweep, median SVG runtime was about 21% of Go's for Dagre and ELK, and 47% for TALA. Median peak memory was lower too, and no measured row was slower than Go. Every timed run also had to produce identical output. The other export formats were faster on the measured corpus as well.

The WebAssembly result was especially satisfying given why I wanted this in the first place. In Node, the Rust build produced identical SVGs on all 36 benchmark rows and took about 27–33% of Go's total runtime, depending on the engine. The raw module is about 31 MB instead of 60 MB, starting in about a tenth of the time. The native executable is larger than Go's, though: about 49 MB against 41 MB, stripped on Linux arm64. The [benchmark notes][benchmarks] cover the methods and limits.

Both arm64 and Linux x86-64 have been checked against Go built for the corresponding architecture. Windows passes the default test tier and all 944 golden boards natively with MSVC and under Wine. All 67 applicable upstream CLI assertions pass, along with the retained CLI goldens that the pinned Go still reproduces. Differential testing covered 5,024 original jobs; five mutated inputs crash the pinned Go, while d2-rs reports an error instead.

## Matching the awkward details

Two layout engines can draw the same diagram and still get there differently. Sometimes that difference is harmless. Sometimes it is the thing that makes the next diagram go wrong.

One early example came from September, when the port still targeted Go D2 v0.9.0. Codex did the investigation and implementation described here.

### The same children in a different order

One investigation involved a diagram with nested containers, grids, and arrows running in several directions. The Rust and Go gap-connectivity traces disagreed. That trace records how the layout engine discovers connected nodes while deciding which spaces it can adjust.

The objects were there. The edges were there. The problem was which view of the children the traversal used.

Go's connected-node walk could still see a container's retained child slice after a nested placement graph had projected those children into a different order. The Rust code had the active children available, but walking that list did not always reproduce what the Go objects still exposed.

That changes the sequence of decisions the algorithm makes. Translating the obvious graph structure wasn't sufficient; the retained view was part of the behavior too.

The fix had to preserve that order when the retained entries actually covered the active children, while falling back to the active slice for stale partial lists. Extracted children still needed to be traversable. An earlier version of that handling exposed another two-node order mismatch, so always preferring the old list wasn't a useful shortcut either.

After the correction, the nested-direction case matched the complete normalized gap trace at six seeds. Each run contained roughly 31,000 events. That gave us a way to check the sequence of decisions behind the matching pictures.

### When Go disagrees with itself

There was another wrinkle in the same corpus. A sequence-diagram case produced different gap traces across fresh Go processes, even with the seed held fixed. Rust's trace stayed stable, and the public graph output stayed stable in both implementations.

The source walks a Go map in that path. Repeating the reference executable was necessary to distinguish its varying traversal order from a stable mistake in the translation.

The comparison kept those differences visible. Sorting every event would have removed the ordering information that had just helped find the real bug. It could identify a demonstrated map-order variation without claiming that the raw execution streams matched.

What I liked about this investigation was how concrete the failure became. “The diagram is different” leaves a lot to search through. “This connected-node walk is reading a different child list” gave Codex something specific to fix.

## What the tests missed

Going beyond the upstream corpus was necessary. Generated cases and mutations exposed gaps in code highlighting, extreme sizes, small shapes, text rendering and error handling. Some large TALA cases still use more memory or take longer than Go; those are tracked in the [known issues][issues].

On November 7, I asked Claude to render an isometric sample from upstream. The SVG and PNG both matched the pinned Go byte for byte, but the SVG took about 7.63 seconds against Go's 0.77 seconds. PNG was faster, about 0.70 seconds against 0.82 seconds. The performance suite hadn't exercised the isometric option. Even after expanding the benchmarks, a normal request to render a picture found another missing case.

I'm excited to have this out! I wanted D2 in Rust and WebAssembly, and now there's a version people can actually install and use. After two months of work, seeing it render the same diagrams and run faster on the measured corpus is really satisfying. I hope it's useful to other people too.

Thanks to the D2 team for the language and the test suites that made this work possible. Questions and feedback are welcome on the [community announcement][announcement].

[d2]: https://www.d2lang.com/tour/intro/
[crate]: https://crates.io/crates/d2-rs
[npm]: https://www.npmjs.com/package/@d2-rs/wasm
[announcement]: https://github.com/d2lang/d2/discussions/2958
[repo]: https://github.com/nelsonjchen/d2-rs
[benchmarks]: https://github.com/nelsonjchen/d2-rs/blob/main/docs/BENCHMARKS.md
[issues]: https://github.com/nelsonjchen/d2-rs#known-issues
[go-ports]: https://github.com/nelsonjchen/d2-rs#go-behavior-ported
