---
title: "Surviving a power outage with fiber ONT and PoE setup (Frontier FOX222)"
date: 2025-07-06T00:00:00Z
tags:
  - fiber
  - ont
  - poe
  - setup
  - power outage
  - frontier
  - ecoflow
  - ups
---

![fox222](https://assets.mindflakes.com/fiber-poe/fox222.jpg)

![power supply](https://assets.mindflakes.com/fiber-poe/CA25U16V2.jpg)

For some reason, it's been a bit hard to find information on how to setup my particular fiber ONT so that it can survive a power outage with a PoE setup.

While of course, one could power a whole house with solar and batteries, I wanted to find a way to keep my fiber ONT and router powered during a power outage without having to invest in a full home battery system.

Costco recently [had on sale a small EcoFlow RIVER 3 Plus Portable Power Station](https://slickdeals.net/f/18368635-ecoflow-river-3-plus-wireless-boost-combo-219), which I installed into my home network setup with the power station being next to my router.

It's been wonderful as an UPS!

My setup is a bit peculiar as my Frontier FOX222 ONT is a bit far from the power station, but the power station is next to the router and there is an Ethernet cable running from the ONT to the router. The RIVER 3 is also next to the router.

The only way to provide power to the ONT without also having to plumb AC backed by the power station: A PoE injector on the Ethernet cable between the ONT and the router and a splitter at the ONT end to convert the PoE to 16V DC power for the ONT.

The FOX222 ONT is powered by a 16V DC power supply. There is an "Auxiliary Power Supply" port on the side of the power supply. Unfortunately, there is no printed information on what acceptable inputs are acceptable for this port. Nor is there documentation on the power supply itself on acceptable voltages. And size, that took forever and was inconsistent.

Information is also a bit scattered and/or some people have done permanent modications. Some [sources have even said 12V is all you need but this is wrong](https://www.instructables.com/I-211M-L-ONT-Enable-Data-While-on-Battery-Power/)! The information may be applicable to a different ONT model.

Through some trial and error and quite some Amazon returns, I've have an Amazon checklist of the requirements for what is needed.

My environment:

* Frontier FOX222 Fiber ONT (Optical Network Terminal)
* Cyberpower CA25U16V2 Power Supply

Requirements:

* You need a PoE injector that can provide at least 30W. The power supply itself at the end of the chain is rated for 16V at 1.6A but it is unlikely to use the full 1.6A.
  * [TRENDnet Gigabit Power Over Ethernet Plus Injector, PoE+ (30W)](https://amzn.to/3Iubo63)
* You need a PoE splitter that can provide 16V DC output or something close to it. The FOX222 ONT requires 16V DC input. It will not run with 12V DC input. The 18V DC output is within range and works.
  * [LINOVISION 30W Gigabit PoE Splitter to DC 5/9/12/18V Output, Wide Voltage Input](https://amzn.to/4ew8v0C)
* The power supply connector has a DIN-like connector on one end. It must be modified so that when there is no AC power, the ONT will still power on and provide data.
  * The modification is easily reversible with a small piece of alumnium foil.
  * ![Modification Image](https://assets.mindflakes.com/fiber-poe/power-supply-plug-modification.webp)
  * Image from https://www.instructables.com/I-211M-L-ONT-Enable-Data-While-on-Battery-Power/
  * If not modified, apparently the ONT will refuse to power on the data port. I did not care to try.
* The "Auxiliary plug to the the power supply is a 4.0mm x 1.7mm barrel connector. I don't know why some some sources say it's a 4.5mm x 3.0mm barrel connector. Or [have other bizzare sizes such as "1.3mm x 3.5mm".](https://www.instructables.com/I-211M-L-ONT-Enable-Data-While-on-Battery-Power/)
  * [4.0mm x 1.7mm barrel connector](https://amzn.to/4eKLUgX)
* You're adding some Ethernet cable segments. Some small thin cables is fine.
  * [Monoprice's SlimRun cables seem to be excellent. I have a few 1 ft patch cables](https://amzn.to/4nyWHyP)

Installation:

* Plug the PoE injector into the wall and connect it to the router on the WAN side. Use the short Ethernet cables to add small segments as needed.
* Connect the PoE splitter to the ONT on the other end of the Ethernet cable. Use the short Ethernet cables to add small segments as needed.
* The LINOVISION PoE splitter has a 5.5mm x 2.1mm barrel connector male as an output. Connect it to the PoE adapter's cable with the 4.0mm x 1.7mm barrel connector.
* Plug the 4.0mm x 1.7mm barrel connector into the "Auxiliary Power Supply" port on the Cyberpower CA25U16V2 power supply.
* Take out the wire between the ONT and the power supply and modify the plug as shown in the image above. This is done by stuffing in a small piece of aluminum foil into the barrel connector. It apparently shorts some pins together to allow the ONT to power on and provide data when there is no AC power.
  * ![Modification Image](https://assets.mindflakes.com/fiber-poe/power-supply-plug-modification.webp)
* Unplug the AC cord from the Cyberpower CA25U16V2 power supply.
* Plug in the DC barrel connector into the power supply's "Auxiliary Power Supply" port.
  * ![Auxiliary Power Supply](https://assets.mindflakes.com/fiber-poe/auxilllary_power_supply.jpg)
* It should power on!
* **Do not plug in the AC cord!** as if you plug it in, the ONT will reboot. This is actually bad. If the AC power goes out, the ONT will reboot. Just leave it powered by the DC power supply exclusively and from the PoE injector.

Setup Image:

* ![Setup Image](https://assets.mindflakes.com/fiber-poe/setup.jpg)

Operation:

* Leave the AC cord from the power supply unplugged. If you plug it in, power outages will disrupt service. You're welcome to test this yourself once you have the setup working completely on DC power.

Things I don't know:

* Does this work the with the FRX523? It's apparently some sort of successor to the FOX222.
* Will there by long term issues with the ONT being powered by a 18V DC power supply?

Comments:

Questions and suggestions are welcome to https://bsky.app/profile/mindflakes.com .

References:

* https://www.dmdtech.org/2024/11/30/power-your-frontier-and-others-ont-w-poe/
  * Gave a hint that 16V is an absolute requirement
  * Provided a hint to the LINOVision PoE splitter's 18V output being acceptable

* https://www.instructables.com/I-211M-L-ONT-Enable-Data-While-on-Battery-Power/
  * Provided the image above
  * Implies that the power supply is happy to take a 12V DC input but this is
  * Plug size is wrong, it's 4.0mm x 1.7mm!
  * Only thing right seems to be the modification of the power supply plug with aluminum foil