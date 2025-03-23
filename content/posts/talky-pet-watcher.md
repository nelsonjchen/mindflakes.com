---
title: "Talky Pet Watcher, a cat watching AI"
date: 2025-02-01T06:08:55Z
tags:
  - catte
  - google_gemini
  - vision
  - telegram
---

![Cat Watch](/images/talky-pet-watcher.png)

Github Project: https://github.com/nelsonjchen/talky_pet_watcher/

I'm allergic to cats 😢. But a friend asked me to watch their cat. My family was more than willing to help but to be safe, I wanted to also monitor the cat to make sure she didn't get into any shenanigans that were too much.

With some cheap TP-Link Tapo cameras, I set up a system to watch the cat remotely and ensure she was safe while I could not be next to her. It's a telegram bot that also sends me a message whenever the cat is doing something interesting.

It watches multiple cameras and tries to aggregate the data to provide insights on the cat's behavior.

Here's the project description:

> Talky Pet Watcher is a tool to watch a series of ONVIF webcams and their streams. It captures video clips, uploads them to Google AI for processing, and uses a generative model to create captions and identify relevant clips. The results are then delivered to a Telegram channel. This project is designed to help pet owners keep an eye on their pets and share interesting moments with friends.

This was slapped together in a few days, as I only watched the cat for two weeks.

I was also interested in the Google Gemini AI and how cheap they claimed to be for analyzing video. I figured that this would be a good way to test it out.

What went well:

* The system was able to capture interesting moments effectively.
* It was able to concantate multiple camera's POV to provide interesting views.
* The cameras were cheap!
* Putting the results on Telegram was a good way to share the results with the cat's owner and friends.

What did not go well (and there are many!):

* I did not implement history, so the system basically reported the same thing over and over again.
* Getting reliable clips from multiple cameras when motion was detected was iffy due to the TP-Link Tapo camera's iffy web servers. It would stall and halt a lot.
* The Google Gemini AI could only analyze video clips at 1FPS. For an agile kitten, this can sometimes make wrong assumptions about what the cat is doing.
* Implementation wise, `bun` was very crashy and I had to restart it a lot.
* The connection to the camera also had to be restarted a lot.

If I had to do it again:

* Use something that can pull from a NVR by relative timestamps. There was some Rust NVR but it was too complicated for me to set up in the small amount of time I had.
* Implement a history system and maybe some memory bank system.
* And so so much more! 😅

