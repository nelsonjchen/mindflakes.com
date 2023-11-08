---
title: "Openpilot Replay Cliper"
date: 2022-10-01
tags:
  - openpilot
  - replay
  - comma_ai
  - openpilot_e2e_long
  - digitalocean
---

Replicate: https://replicate.com/nelsonjchen/op-replay-clipper

https://github.com/nelsonjchen/op-replay-clipper/

📽 Capture short ~30 second route clips with the Openpilot UI included, and the route, seconds marker branded into it.

Designed to try to encourage more interesting clips to be shared, especially with newer functionality such as "end to end longitudinal control".

Originally a bit heavy, so instructions were made for others to run it on services like DigitalOcean/GitHub Codespaces. Since then, the clipper has been ported to Replicate which provides GPUs and a nice WebUI.

The UI rendering is composed of a shell script and a Docker setup that fires up a bunch of processes and then kills them all when done. On Replicate, it is wrapped by a Python script that handles the UI and non-UI processing of video and downloads.
