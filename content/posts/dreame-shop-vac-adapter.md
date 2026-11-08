---
title: "Can a shop vacuum empty my robot vacuum?"
date: 2026-11-08
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

My Dreame L50 Ultra can pick up dog hair. Getting a dense wad of that hair out of the robot and into the dock's bag is another problem. I started wondering whether a separate wet/dry vacuum could do the emptying instead.

I directed the project and supplied the measurements and physical fit-test feedback. Codex did the CAD implementation and Home Assistant automation work, and helped draft this post. The printed parts and physical tests were my side of the collaboration.

{{< figure src="/images/dreame-shop-vac-printed-adapters.webp" alt="Two green 3D-printed parts, a flat dock adapter with a curved outlet and a separate hose elbow, beside the vacuum hardware." caption="The printed dock adapter and separate elbow. The CAD versions for both wet/dry vacuums are shown below." link="/images/dreame-shop-vac-printed-adapters.webp" >}}

We had two wet/dry vacuums available: a **Stanley SL18129** and a **DeWalt DXV05P-QT**. Their hoses have different connectors, so Codex made a version for each. The [project repository][repo] has the editable build123d source and downloadable STL and STEP models.

This is still an experiment. I've printed parts, checked some fits and tested the switching sequence, but I haven't established that the complete assembly seals or reliably pulls the debris out.

## Keeping the dock intact

The idea is to keep the robot and dock intact. The robot empties through the dock's existing evacuation path, then a printed adapter routes that path into a short hose connected to the wet/dry vacuum. I wanted a removable part at the bag interface, rather than cutting a hole in the dock and discovering afterward that I'd misunderstood the airflow.

There are more questions here than “can I make two holes line up?” The dock detects whether a bag is present. The adapter needs to sit properly in its slot, keep a seal, leave room for the hose, and remain removable. More suction also isn't automatically better for the robot's small bin, flaps, and seals. An adjustable air bleed is part of the prototype plan, and the useful suction level still needs measuring.

Codex used [build123d][build123d] for the parametric CAD. The editable model records the dimensions and clearances, so changing a hose measurement doesn't mean starting the whole part over. I could print small fit-test pieces before committing to a larger adapter with an elbow attached.

## Two vacuums, two versions

The dock interface is shared, but the hose side changes. Each version has two pieces: the card adapter that slides into the dock's bag slot, with its first bend, and a separate plug-in 90-degree elbow to turn the hose connection downward. Making the second bend removable let us change that routing without reprinting the whole card adapter.

The Stanley version started from a roughly 30.75 mm hose connector. A separate tapered socket coupon fitted that connector well. The first dock card could wiggle out; later revisions changed the trailing outline, and the third version fitted much better. Those small test pieces answered fit questions before I committed to larger prints.

{{< figure src="/images/dreame-stanley-adapter-render.webp" alt="CAD render of the Stanley SL18129 version: a green dock card adapter on the left and its separate plug-in elbow on the right." caption="Stanley SL18129: the REV 5 dock adapter and matching Stanley-to-Stanley elbow, rendered from the actual CAD meshes. The pieces are separated for visibility." link="/images/dreame-stanley-adapter-render.webp" >}}

The DeWalt needed its own measurements. Its cuff measured 44.77 mm at the tip and 45.77 mm at the wider end over 62.24 mm. Codex used that taper for the portion inserted into the socket, with clearance added explicitly, and enlarged the bend for the bigger connector. A nominal hose size wasn't enough to design the actual mating surface.

{{< figure src="/images/dreame-dewalt-adapter-render.webp" alt="CAD render of the DeWalt DXV05P-QT version: a yellow dock card adapter with a larger outlet on the left and its matching separate elbow on the right." caption="DeWalt DXV05P-QT: the measured-cuff REV 5 adapter and matching DeWalt-to-DeWalt elbow. These are CAD renders, not photos of printed parts." link="/images/dreame-dewalt-adapter-render.webp" >}}

There is also a Stanley-to-DeWalt elbow in the source. Having both vacuums meant this became a small family of related models, rather than one fixed hose size. The CAD checks confirm valid solids and clearance between the matching parts; printed joint fit, retention, sealing and dock-cover clearance still need their own checks.

## Switching the motor with Home Assistant

In parallel, Codex configured Home Assistant to switch the vacuum's smart plug around the Dreame's auto-empty cycle. An early end-to-end test confirmed the robot could clean, return, report its emptying state, and produce the expected relay on/off sequence. The relay stayed on for about 46 seconds in that test. This checked the automation and switch states, not dust capture through the printed part.

This uses the [Dreame Home Assistant integration I worked on]({{< relref "dreame-l50-home-assistant.md" >}}) to follow the robot's status.

The timing has changed since then. The current configuration follows the dedicated auto-empty status: wait six seconds after it becomes active, check that it's still active, then turn the relay on. When emptying becomes idle, turn it off. That revision was saved and read back, but hasn't had another physical emptying test.

The plug also has its own two-minute cutoff and boots with its relay off. It doesn't need Home Assistant or Wi-Fi to enforce that timer. The cutoff's fault path still needs a deliberate hardware test.

So there are two projects meeting in the middle: a mechanical route for the hair and a control sequence for the motor. The smaller printed pieces have helped with fit, and the automation has a real sequence test behind it. The next useful result is a supervised test of the assembled, sealed airflow path. Until then, I have a promising adapter experiment, not a demonstrated cure for the hairball problem.

If you have a similar problem, the [source and model files][repo] should give you a starting point. The useful part of keeping it parametric is that a different hose can become another variant, using its actual measurements.

[build123d]: https://build123d.readthedocs.io/en/latest/
[repo]: https://github.com/nelsonjchen/wet-dry-vac-to-dreame-l50-ultra-adapter
