---
title: "Replicate.com Openpilot Replay Clipper"
date: 2023-10-12T00:00:00Z
tags:
  - openpilot
  - replay
  - replicate
  - openpilot_e2e_long
  - comma_ai
---

![Web capture_8-11-2023_9551_replicate com](https://github.com/nelsonjchen/op-replay-clipper/assets/5363/7a9cf016-2012-4acd-90e9-9cdec6bf96c1)

The replay clipper has been ported to [Replicate.com](https://replicate.com)!

https://replicate.com/nelsonjchen/op-replay-clipper

Along with it comes a slew of upgrades and updates:

* **GPU accelerated** decoding, rendering, and encoding. NVIDIA GPUs are provided to the Replicate environment and greatly speed up the clipper.
* Rapid fast downloading of clips. Instead of relying on `replay` to handle downloads sequentially in a non-parallel manner, we use the parfive library to download underlying data in parallel.
* Comma Connect URL Input. No need to mentally calculate the starting time and length. Just copy and paste the URL from Comma Connect.
* Video/UI-less mode. Don't want UI? You can have it.
* 360 mode. Render 360 clips
* Forward Upon Wide. Render clips with the forward clip upon the wide clip
* Richer error messages to help pinpoint issues.
* No more having to manage GitHub Codespaces. Replicate handles all the setup and cleanup for you.

Unfortunately, there is a cost. It's a very small cost but technically Replicate.com is not free. Expect to drop a cent per render. Thankfully, you have a lot a trial credits to burn through and the clipper can run on a free-ish tier.