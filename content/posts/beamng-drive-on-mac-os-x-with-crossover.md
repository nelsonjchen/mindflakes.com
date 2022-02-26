---
title: "BeamNG DRIVE on Mac OS X with CrossOver"
date: 2013-08-03 16:06:37-07:00
aliases:
  - /blog/posts/2013/8/3/beamng-drive-on-mac-os-x-with-crossover/
tags:
  - beamng
  - wine
  - mac
---

**UPDATE** 2015-11-28: Hey BeamNG forum users. These instructions worked for me way
back when I was using CrossOver. You might be able to get similar or better results
with Wineskin Winery or some other Wine wrapper. And those are *free* too!
I don't run BeamNG on my local laptop anymore nowadays but I [Game on Amazon EC2][]
and it works fairly well with BeamNG at a very minimal cost.

![beamng on crossover](/images/beamng-crossover.jpg)

Gotta make this quick. This post will change.

Here's how to get [BeamNG Drive][] working in Mac OS X. I'm sure there are equivilent instructions for [Wineskin][]
but I have no time to find out.

Make a new bottle in Crossover. Make sure it is a Windows Vista bottle. Run the Wine configuration setting inside it
and set the version to Windows 7 in one of the sheets. Enable the Mac Driver so you don't run it through X11.

Install the Modern DirectX runtime into it. Install BeamNG into it. Mute your speakers. Change the audio options to
use OpenAL for output. Unmute speakers. Enjoy.

So far this works for the tech demo. I'm going to try it on Alpha later.

UPDATE: It works on Alpha. Yay!

[Game on Amazon EC2]: (http://lg.io/2015/07/05/revised-and-much-faster-run-your-own-highend-cloud-gaming-service-on-ec2.html)
[BeamNG Drive]: http://www.beamng.com/drive/
[Wineskin]: http://wineskin.urgesoftware.com/tiki-index.php?page=Wineskin%2C+Play+your+favorite+Windows+games+on+Mac+OS+X+without+needing+Microsoft+Windows
