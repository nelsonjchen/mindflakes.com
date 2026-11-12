---
title: "Solving my Dreame L50 Ultra's hairball problem with a 3D-printed adapter, wet/dry vac, and Home Assistant"
date: 2026-11-08
lastmod: 2026-11-12
draft: false
slug: "dreame-shop-vac-adapter"
tags:
  - dreame
  - 3d-printing
  - home-assistant
  - build123d
  - codex
  - ai-assisted
---

{{< figure src="/images/dreame-shop-vac-full-setup.webp" alt="Dreame L50 Ultra dock and robot on the left, with a green printed adapter connected by a loop of black hose to the yellow DeWalt wet/dry vacuum under a table on the right." caption="The full setup: the Dreame dock on the left, connected to the DeWalt under the table on the right. The wheels are off to make it fit; the hose is still longer than it needs to be." link="/images/dreame-shop-vac-full-setup.webp" >}}

My [Dreame L50 Ultra][dreame] could pick up dog hair, but its dock struggled to get the dense wads of hair out of the robot and into the bag. I made an adapter so an external wet/dry vacuum could do the emptying instead, with [Home Assistant](https://www.home-assistant.io/) handling the switching. The project is finished and in use, and I haven't had a hairball since!

I directed the project, took measurements, and printed and fit-tested the parts. [Codex](https://openai.com/codex/) did the CAD implementation and Home Assistant automation work, and helped draft this post.

The replacement bags annoyed me too. I was replacing them almost every week. Three hairy doggies shed a lot! [Bags like these on Amazon][bags] were about fifty cents to a dollar each. Getting them cheaper on AliExpress is an option, but it still sucks when a hairball gets stuck and the bag is only half full. Throwing away a half-full disposable bag means more waste going to landfill, on top of paying for capacity I wasn't getting to use. Cheaper bags wouldn't fix the dock's inability to get the hair into them.

The bag-change reminder wasn't much help with the clogs. It just runs on an interval, and sometimes it hadn't appeared even though the bag was already clogged. I'd find out when the robot complained about tangling, by which point the bag evacuation system had already been jammed for a while. I doubt there's a working bag-full sensor here.

I also looked at [this reusable dust-bin design on MakerWorld][reusable-bin]. I didn't think it would be enough for my setup: the bins were small, and replacing the bag with a rigid container wouldn't address the hairballs clogging the evacuation path. The design also calls for cutting up a vacuum bag and using its filter material in the printed bin, so it still means sacrificing a bag for filtration.

I already had a **Stanley SL18129** on hand, so I started with that. I found it loud, though, and bought a quieter [DeWalt DXV05P-QT][dewalt]. Their hoses have different connectors, so Codex made a version for each. The DeWalt is the one I use now. The [project repository][repo] has the editable build123d source and downloadable STL and STEP models.

{{< figure src="/images/dewalt-dxv05p-qt.webp" alt="Yellow and black DeWalt Stealthsonic wet/dry vacuum with a top carrying handle and caster wheels." caption="The quieter DeWalt DXV05P-QT I bought to replace the Stanley. Product photo." class="product-photo" width="320" link="https://link.amazon/B01E7xj0y" >}}

## Keeping the dock intact

The robot and dock stay intact. The robot empties through the dock's existing evacuation path, then a printed adapter routes that path into a short hose connected to the wet/dry vacuum. I wanted a removable part at the bag interface, rather than cutting a hole in the dock and discovering afterward that I'd misunderstood the airflow.

{{< figure src="/images/dreame-shop-vac-printed-adapters.webp" alt="The green Stanley adapter rests loose on the left inside the dock cavity; the DeWalt adapter is installed on the right and connected to its hose." caption="The Stanley adapter is resting loose in the dock cavity on the left. The DeWalt adapter is currently connected on the right. The DeWalt CAD model is shown below." link="/images/dreame-shop-vac-printed-adapters.webp" >}}

The design involved more than making two holes line up. The dock detects whether a bag is present, and the adapter had to sit properly in its slot, keep a seal, leave room for the hose, and remain removable.

The MakerWorld bin was useful as a starting reference, though. We took measurements of its inlet hole and mating interface, recreated that geometry in [build123d][build123d], then fine-tuned my adapter through measurements and fit tests to meet my needs. That gave us somewhere to start with the dock connection; the hose routing and vacuum-specific connectors grew from there.

The [build123d model in the project repository][repo] keeps the dimensions and clearances editable, so changing a hose measurement doesn't mean starting the whole part over.

## The DeWalt adapter

I started with an [adapter for the Stanley](https://github.com/nelsonjchen/wet-dry-vac-to-dreame-l50-ultra-adapter/blob/main/docs/images/stanley-adapter.webp), then had Codex modify it for the DeWalt when I switched vacuums. The dock connection stayed the same; the hose connector and bend needed changing. Here's the [DeWalt version][repo] I use now.

It has two pieces: the card adapter that slides into the dock's bag slot, with its first bend, and a separate plug-in 90-degree elbow that turns the hose connection downward. Keeping the elbow removable let us adjust the routing without reprinting the whole card adapter.

The DeWalt's hose cuff widens from about 45 to 46 mm along the inserted portion. Codex modeled that taper with clearance for the fit, and enlarged the bend for the bigger connector. A nominal hose size wasn't enough to design the actual mating surface.

{{< figure src="/images/dreame-dewalt-adapter-render.webp" alt="CAD render of the DeWalt DXV05P-QT version: a yellow dock card adapter with a larger outlet on the left and its matching separate elbow on the right." caption="DeWalt DXV05P-QT: the measured-cuff REV 5 adapter and matching DeWalt-to-DeWalt elbow. These are CAD renders, not photos of printed parts." link="/images/dreame-dewalt-adapter-render.webp" >}}

## Switching the motor with Home Assistant

Before this, I worked with Codex on a small maintenance fork of Tasshack's Dreame Home Assistant integration, fixing setup cleanup and checking support for my L50. I wrote about that in [the integration post]({{< relref "dreame-l50-home-assistant.md" >}}). For this project, that integration gives Home Assistant the dedicated auto-empty status to listen to.

When auto-empty becomes active, the automation waits six seconds and checks that it's still active. It then switches on the wet/dry vacuum through a [Sonoff S31](https://www.amazon.com/dp/B08TNF4835) running [ESPHome](https://esphome.io/), repurposed from another job. When the status returns to idle, Home Assistant switches the vacuum off.

I haven't noticed any switching problems with the S31 and this vacuum. That's my experience with this setup; I haven't verified a motor-load rating for the complete plug, so check suitability before copying that part.

That S31 listing is no longer available, but a similar Home Assistant-compatible smart plug rated for the vacuum's motor load can take its place. The [Shelly Plug US Gen4][shelly] is another option to investigate: it supports [local Home Assistant control][shelly-ha] with its stock firmware, and there is also a [community ESPHome conversion][shelly-esphome]. I haven’t tested it with this vacuum setup; check its motor-load rating before choosing it.

{{< figure src="/images/dreame-auto-empty.svg" alt="The Dreame reports auto-empty status to Home Assistant, which controls the S31 relay powering the DeWalt vacuum. Active status triggers a six-second wait and recheck before switching on; idle switches it off. The S31 has a local two-minute cutoff and boots with the relay off." caption="Status from the Dreame controls the vacuum; the timeout runs locally on the S31. Rendered with d2-rs and TALA." link="/images/dreame-auto-empty.svg" >}}

Codex configured the S31 and the automation. The plug exposes power measurements as well as relay control, and ESPHome enforces a two-minute maximum runtime locally. These vacuums draw about 1,000 W, so I wanted a backstop in case Home Assistant goes down before sending the off command. If Home Assistant or Wi-Fi drops out, the plug can still turn the vacuum off when that timer expires. It also boots with its relay off, so a reboot won't restart the motor. The local cutoff isn't required for the automation to work, but I think it's a good idea.

I'm happy with how this turned out, and buying the quieter DeWalt made it easier to live with. If you have a similar problem, the [source and model files][repo] should give you a starting point for your own hose and dock setup.

Only after starting this project did I learn that people also use wet/dry vacuums with relays for trapping rats! [Shawn Woods tested one][rat-trap-video]—an unexpected parallel, though it wasn't where my idea came from.

## Known issues

### It could be tidier

The full setup is a big yellow wet/dry vac next to the dock. I left the DeWalt's wheels off so it fits under a table more compactly, but the dock's front stays open, which looks kind of ugly, and the hose is longer than it needs to be. It works, but there's room to make the arrangement much nicer to look at.

### The bag-change reminder

The Dreame's interval-based bag-change reminder still periodically skips auto-emptying, so the external vacuum doesn't turn on for that cycle either. On the next docking with auto-empty, it resumes working. There isn't a dock bag to replace with this setup, and the reminder doesn't mean the evacuation path has clogged again. This is separate from the dock's check for whether a bag is installed.

[build123d]: https://build123d.readthedocs.io/en/latest/
[repo]: https://github.com/nelsonjchen/wet-dry-vac-to-dreame-l50-ultra-adapter
[dewalt]: https://link.amazon/B01E7xj0y
[bags]: https://link.amazon/B03Y4rZ02
[dreame]: https://link.amazon/B028SS9YH
[reusable-bin]: https://makerworld.com/en/models/1624511-mova-dreame-reusable-dust-bin-no-filters-needed#profileId-1715080
[rat-trap-video]: https://www.youtube.com/watch?v=X_Pu43jwWS8

[shelly]: https://link.amazon/B0h3QXSSU
[shelly-ha]: https://www.home-assistant.io/integrations/shelly/
[shelly-esphome]: https://github.com/automatous-io/shelly-gen4-esphome
