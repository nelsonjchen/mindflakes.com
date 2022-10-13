---
title: "Openpilot Replay Clipper"
date: 2022-10-12T00:00:00Z
tags:
  - openpilot
  - replay
  - docker
  - docker-compose
  - digitalocean
  - openpilot_e2e_long
  - comma_ai
---

End to End Longitudinal Control is currently an "extremely alpha" feature in Openpilot that is the gateway to future functionality such as stopping for traffic lights and stop signs.

Problem is, it's hard to describe its current deficiencies without a video.

So I made a tool to help make it easier to share clips of this functionality with a view into what Openpilot is seeing, and thinking.

<video controls>
<source src="https://user-images.githubusercontent.com/5363/188810452-47a479c4-fa9a-4037-9592-c6a47f2e1bb1.mp4
" type="video/mp4">
</video>

https://github.com/nelsonjchen/op-replay-clipper/

It's a bit heavy in resource use though. I was thinking of making it into a web service but I simply do not have enough time. So I made instructions for others to run it on services like DigitalOcean, where it is cheap.

It is composed of a shell script and a Docker setup that fires up a bunch of processes and then kills them all when done.

Hopefully this leads to more interesting clips being shared, and more feedback on the models that comma.ai can use.
