---
title: "Unofficial MakerWorld PMM OpenSCAD Reference"
date: 2026-05-04T08:15:00-07:00
slug: "makerworld-pmm-openscad-reference"
tags:
  - bambu
  - makerworld
  - openscad
  - 3d-printing
  - codex
---

I made an [unofficial MakerWorld PMM OpenSCAD reference][pmm-reference-repo].

The short version is that I wanted [Codex][openai-codex] to help me make MakerWorld-customizable [OpenSCAD][openscad] models, and I did not want it guessing from old forum posts, UI behavior, and whatever I happened to remember at 1 AM.

This started because I wanted to use agents on my own OpenSCAD projects without re-teaching the same MakerWorld-specific weirdness every time.

## The documentation problem

MakerWorld's [Parametric Model Maker][pmm-v11] is genuinely useful. [OpenSCAD][openscad] plus a web customizer is a great fit for 3D-printable stuff where the interesting part is "same idea, slightly different dimensions."

If you have not run into it before, [OpenSCAD][openscad] is CAD by writing code. You write variables, modules, and geometry operations, and it turns that into a model. That makes it very good for customizable prints, and very annoying when a target platform has extra rules.

Unfortunately, the OpenSCAD side of PMM is documented in a very internet way. Some of it is in release posts. Some of it is in support replies. Some of it is in the actual web app. Some of it is people trying things and reporting what happened.

There is a Bambu forum thread literally titled [Any Documentation on Parametric Model Maker?][pmm-docs-thread]. The first post asks where to find the requirements for making customizable models. That felt familiar.

For a human, scattered docs are annoying. For a coding agent, they are a great way to get plausible garbage. It might write OpenSCAD that looks fine locally but misses MakerWorld's actual rules. It might invent a PMM feature because it saw something sort of similar somewhere else.

I do not want an agent inventing `// preview[...]` as a PMM feature. I want it to know that `// color` is a thing, `// font` is a thing, `mw_plate_N()` is a thing, and `default.svg` is not just some random example filename.

So I made a reference.

## What I collected

The repo is basically a practical map of the PMM OpenSCAD surface:

- the [PMM OpenSCAD API][pmm-api]
- an [agent workflow][pmm-agent-workflow] for converting normal OpenSCAD into PMM-ready OpenSCAD
- [gotchas][pmm-gotchas] that agents are likely to mess up
- compatibility rules
- public source snapshots
- patterns and checklists
- a generated docs site

The small examples are the important ones:

```scad
accent = "#FF0000"; // color
font_name = "Roboto"; // font

module mw_plate_1() {
    // printable plate here
}

module mw_assembly_view() {
    // preview-only assembly here
}

svg_file = "default.svg";
```

None of that is hard once you know it. The problem is making sure the agent knows it before it starts "helping."

I also made a [PMM font index][pmm-font-index]. It has 1881 MakerWorld PMM font families and 8267 exact PMM font strings. This got more elaborate than I expected, but fonts are exactly the kind of thing where an agent can confidently choose something that works on your machine and then does not work where the model actually runs.

## Why agent-first

This is not meant to replace the official PMM UI or be a polished tutorial for someone's first customizable model. It is mostly a pile of receipts and instructions for agents.

Point [Codex][openai-codex] or another coding agent at the repo, then ask it to adapt a `.scad` file for MakerWorld. The repo gives it rules to retrieve before it starts editing:

- flatten local includes when PMM probably will not have them
- do not remove bundled PMM libraries like BOSL2 just because arbitrary local includes are risky
- use PMM upload defaults like `default.svg` and `default.stl`
- add `// color` and `// font` only where those controls are intended
- treat multi-plate output as a publishing decision, not just a module name
- cite whether a claim came from an official app endpoint, official release, employee reply, community report, or inference

That last part matters. I do not want a pile of agent-generated confidence. I want the agent to say why it thinks something is true.

## The repo

The project is here:

https://github.com/nelsonjchen/unofficial-makerworld-parametric-model-maker-openscad-docs

The generated docs site is here:

https://nelsonjchen.github.io/unofficial-makerworld-parametric-model-maker-openscad-docs/

This is unofficial, not endorsed by Bambu Lab or MakerWorld, and not a license grant for any font, library, model, or web asset. It is practical documentation from public sources and reverse-engineering.

It is also probably incomplete or wrong in places. PMM keeps changing, and some of this stuff only becomes obvious after someone trips over it.

Anyway, this mostly exists so my agents stop making the same MakerWorld OpenSCAD mistakes. If it saves someone else from digging through forum threads while trying to publish a customizable model, great.

[openai-codex]: https://openai.com/codex/
[openscad]: https://openscad.org/
[pmm-agent-workflow]: https://nelsonjchen.github.io/unofficial-makerworld-parametric-model-maker-openscad-docs/agent-workflow/
[pmm-api]: https://nelsonjchen.github.io/unofficial-makerworld-parametric-model-maker-openscad-docs/pmm-openscad-api/
[pmm-docs-thread]: https://forum.bambulab.com/t/any-documentation-on-parametric-model-maker/230605
[pmm-font-index]: https://nelsonjchen.github.io/unofficial-makerworld-parametric-model-maker-openscad-docs/font-index/
[pmm-gotchas]: https://nelsonjchen.github.io/unofficial-makerworld-parametric-model-maker-openscad-docs/gotchas/
[pmm-reference-repo]: https://github.com/nelsonjchen/unofficial-makerworld-parametric-model-maker-openscad-docs
[pmm-v11]: https://forum.bambulab.com/t/parametric-model-maker-v1-1-0-major-ui-refresh/203564
