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

https://github.com/nelsonjchen/op-replay-clipper/

📽 Capture short ~30 second route clips with the Openpilot UI included, and the route, seconds marker branded into it.

Designed to try to encourage more interesting clips to be shared, especially with newer functionality such as "end to end longitudinal control".

A bit heavy, so instructions were made for others to run it on services like DigitalOcean.

It is composed of a shell script and a Docker setup that fires up a bunch of processes and then kills them all when done.
