---
title: "comma three faux touch keyboard"
date: 2024-12-13T00:00:00Z
tags:
  - openpilot
  - replay
  - ch552g
  - comma_ai
  - hardware
---

# 🦾 comma three Faux-Touch keyboard

*Long arms for those of us with short arms from birth or those who can't afford arm extension surgery!*

![touchkey keyboard demo](https://github.com/nelsonjchen/c3-touchkey-keyboard/assets/5363/d9617916-2442-4287-b430-709dad173da8)

Built a faux-touch keyboard for the comma three using a CH552G microcontroller. The keyboard is off the shelf and reprogrammed to emulat a touchscreen.

It's a bit of a hack but it certainly beats [gorilla arm](https://en.wikipedia.org/wiki/Touchscreen#%22Gorilla_arm%22) syndrome while driving.

For $5 of hardware, it's very hard to beat.

The journey started in January 2024 and ended in May 2024. Though it, I had to struggle with:

* CH552G programming
* Linux USB HID Touchscreen protocol
* Instructions and documentation

It's also been through that period of unintended usage. The [Frogpilot](https://github.com/FrogAi/FrogPilot) project has had users who were surprisingly interested in using it as an alternative for some GM vehicles where there is no ACC button. They use the keyboard to touch the screen where ACC would be.

Part of the work also meant upstreaming to the the [CH552G community](https://github.com/wagiminator/MCU-Templates). While I doubt it'll have any users, it's nice to give back to the community. There is now a touchscreen library for the CH552G.

The project is open source and can be found at:

https://github.com/nelsonjchen/c3-faux-touch-keyboard

There's a nice readme that explains how to build and flash the firmware. 