---
title: "My wobbly VHS-Decode pipeline"
date: 2026-09-13T10:34:51-07:00
draft: false
slug: "my-wobbly-vhs-pipeline"
tags:
  - vhs
  - preservation
  - codex
  - automation
  - ai-assisted
---

I wanted to recover some VHS tapes. Somehow this turned into tapping a signal inside a Sony VCR, putting capture cards in a computer from the junk pile at work, spreading the processing across three machines, and asking Codex to help redesign an amplifier PCB.

I built this VHS pipeline with the assistance of Codex. I supplied the ideas, directed the work, and handled the physical hardware; Codex did essentially all of the technical investigation and custom coding for my setup. The pipeline builds on the existing projects credited below. Codex also drafted this post.

The usual way to digitize VHS is to connect a VCR's video and audio outputs to a capture device and record them on a computer. The VCR reads the tape and turns its signal into a picture before the computer ever sees it.

For better results, you can look for a nicer VCR with a built-in time base corrector, or TBC, which helps correct playback timing errors that make the picture wobble. Unfortunately, the decks I was looking at were $$$. With ancient media having a moment again, it feels like everybody wants the same aging hardware.

[VHS-Decode](https://github.com/oyvindln/vhs-decode) offers a more hardcore route. You still need a working VCR, but you tap the RF signal inside it, near the tape-head amplifier, before the machine turns it into ordinary video. This is not the antenna connector on the back. Suitable capture hardware records that earlier signal, and the computer reconstructs the picture, including time base correction, in software.

That sounded pretty appealing: use an ordinary VCR, spend less on a fancy deck, and accept more fiddling. Keeping the RF recording also means I can try different settings or a better decoder later without playing the physical tape again.

I first heard about VHS-Decode about two years ago. I lacked the hardware, time, and initiative to really start. Codex helped me get past a lot of those hurdles. I drove the whole setup through Codex, right down to configuring Ubuntu on the capture machine. It was involved from the beginning, not something I added after getting everything working.

The pipeline repo has an `AGENTS.md` with instructions for Codex and a `journal.md` where we take notes as we go. That journal now runs through months of experiments, failures, and procedures that apparently needed another procedure.

The setup is still a work in progress. The notes trace how it grew from “can I get a signal?” into something that helps me understand and organize the tapes too.

## First, get a picture

I already had the Sony SLV-688HF. The capture computer was a Dell OptiPlex 7010 rescued from the junk pile at work. An old business desktop with room for capture hardware seemed like a reasonable place to start.

In late June and early July, the work was mostly getting that machine ready: Ubuntu installation and configuration, the CXADC driver, capture cards, cables, and figuring out whether the signal coming out of the VCR was useful. I was directing that work through Codex while handling the physical hardware myself.

On July 3, the first input we tried was basically flat, even with the tape playing. Switching from the white BNC input to the red one gave us a much healthier signal. A small detail, but fairly important when the whole project depends on recording something other than nothing.

That day's one-minute test produced about 1.6 GiB of RF data. Decoding it on my MacBook took a little over five minutes in that early test, then produced roughly another 3.2 GiB of intermediate picture data. This was already a hint about where the project was going.

First there was a viewable black-and-white preview, without sound. Then a short color preview.

There was an actual picture. The weird route worked well enough to keep going.

## One minute was manageable. Two hours was different.

The next problem was doing this at tape length. I added a Samsung 980 1 TB NVMe SSD I already had on hand as scratch storage in the capture machine. By August 8, a two-hour-and-two-minute capture had produced about 196 GiB of RF data, plus separately recorded audio. The machine's 250 GB boot SSD was not going to provide comfortable working room for that.

Now storage and transfers were part of the job. The source needed to get onto the storage machine, and the copy needed checking before I treated it as safely transferred. Then came decoding, with another set of enormous files to put somewhere.

I store the captured RF on the NAS in FLAC. Yes, FLAC, the format usually associated with lossless audio. Here it holds the sampled RF signal, not a finished video. The separately captured audio has its own FLAC file. This saves space without throwing away the signal samples I want to decode again later.

I had Codex write custom scripts and capture tooling, under my direction, to FLAC the streams on the fly. That avoids making every capture a huge raw file followed by another whole compression job. The compressed captures still need to get safely onto the NAS, and the tooling has to keep track of whether samples were lost or an encoder failed. Compression is useful; silently losing part of the tape is not.

I tried decoding on the storage machine too. One run was heading toward roughly two days of processing. Later the production decoding moved to a Windows machine. The setup grew into three main jobs on three machines: capture, preservation storage, and decoding/export.

The Windows stage now turns the retained RF recording into intermediate luma and chroma picture files, then makes H.265/AAC copies I can watch. Those convenient video files do not replace the preservation source. Keeping the earlier signal is a large part of why I chose this approach in the first place.

Audio added its own little complication. I capture it separately, and separate devices have separate clocks. Their crystal oscillators can run at slightly different rates, so audio that lines up at the start can drift away from the picture later.

Synchronizing the hardware clocks is one approach. I was okay with resampling the audio to fit the video timeline instead. That means adjusting its timing over the recording, not just moving the whole track left or right and hoping. It also means checking synchronization beyond the beginning.

At this point, “capture a tape” had acquired quite a few more steps than pressing record.

## Then I had to find out what was on it

An August entry in that journal has a useful lesson in why the end of a recording is not necessarily the end of a tape.

A fixed-duration capture can keep recording after a short tape has stopped, wasting a ridiculous amount of space. But looking for static is not enough to decide where useful material ends either. One tape had a static gap followed by another recording. Stopping at the gap would have missed it.

These tapes were recorded over, sometimes only partially. A cassette can contain television, home video, and then more television. Sampling a few places and finding TV does not establish that there is nothing personal elsewhere.

So my rule is to capture the whole tape first and analyze it afterward. I do not stop because the interesting-looking recording ended, television started, or the picture went to static. There could be something else further along. The power meter helps detect the physical end of the tape; figuring out which parts to keep in the viewing copies is a separate, later job.

This became one of the most useful parts of having Codex involved. It has been great at scene analysis, helping with transcription and translation, and writing up what is happening on the tapes. It can help sift the home-video sections out of recorded TV programming and leave a map of the recording alongside the watchable files.

The tapes mix Cantonese, Mandarin, and English, and we found some useful differences between automatic speech recognition (ASR) models. Local Qwen3-ASR 1.7B was a particularly good find for Cantonese and also produced useful Mandarin drafts. On one mixed tape, `gemini-3.5-transcribe-preview` struggled with the noisy Cantonese but recovered English television dialogue and word timing that helped fill gaps.

Codex coordinates those ASR passes, compares their output, and helps turn the results into transcripts and translated subtitles. Trying different models on the actual recordings has been more useful than assuming one model will handle every language and every stretch of old tape equally well.

The review procedures grew around that work: frames throughout the recording, extra scene-change samples, and moving picture with audio around anything ambiguous. I want to know what was actually checked, not just get a confident summary based on three screenshots.

Codex also helped read the wonky Chinese handwriting on the old labels. Some of it is barely readable scratches at this point. A plausible reading and translation can give me a starting point, and the footage can help make sense of it. Uncertain characters need to stay marked as guesses. The same goes for difficult speech in a transcript.

I am keeping the tape contents and detailed records private, but this part matters to the story. Getting a video file is useful. Having help figure out what is in it, where the different recordings start, and how to find them again makes the whole project much more useful to me.

## Of course it grew a labeling system

By mid-August, I had also connected a Brother PT-D600 label printer over USB. Apparently the tape project needed a labeling regime too.

I stopped using printed status labels. Keeping those updated was more fuss than it was worth; off-the-shelf smiley-face stickers from Amazon were easier.

The labels I still print are for identity and content only. Each tape gets a stable ID, and its box gets the same one. Content labels describe the recording, with a date when known. Processing status stays in the digital records, not on a succession of replacement labels.

Yellow means “could be better.” Green is reserved for what I would call a perfect capture. I am not there yet: I still do not have the shared-clock hardware to synchronize the captures, so I am using the resampling approach described earlier. Green is a goal for now, not a claim about the captures I already have.

That connects the digital notes back to the object in front of me. Codex can help inspect a tape, write up the findings, and print the labels. If the box and cassette get separated, I can still identify both.

## Codex can press Play

This is one of my favorite parts of the setup. Codex can operate the VCR itself, with infrared commands to control it and power readings to check what it is doing.

An [M5Stack Atom Lite](https://link.amazon/B006NVWEM), a little ESP32 device with a built-in infrared transmitter, sends the VCR commands like play, stop, and rewind. It cost me $14. I 3D-printed a small stand for cents to hold it up against the VCR, aimed at its infrared receiver.

A power meter supplies the feedback. The deck draws different amounts of power when the tape transport is active and when it is stopped with a tape loaded. The workflow watches those measured states rather than assuming an IR command worked. To rewind, it sends Stop, checks that the deck has settled, sends Rewind, then watches for sustained activity followed by a stable return to the stopped state. Those checks are specific to this VCR and setup.

At capture time, the software starts recording before sending Play, so it does not miss the beginning while getting ready. It then watches for the deck to become active. When the tape reaches its physical end and the transport stops, the power draw settles back into the stopped range. That sustained change is how the workflow detects end-of-tape, alongside the capture's own integrity checks. It records through the TV programming, static, and silence instead of using the content to guess where the tape ends. An unexpected stop or ambiguous meter evidence still needs checking; a power reading alone cannot explain why the transport stopped.

## A revised RF amplifier

Later in August, I had Codex help redesign the RF amplifier PCB for a more ordinary, externally regulated 9V input. I wanted the power setup to be less exotic. This was a later hardware experiment, not something I had to finish before getting that first picture in July.

The redesign passed KiCad electrical and layout checks, and the prototype fabrication order shipped. That is not a bench-tested result yet. Still, going from a project I had not managed to start to ordering a revised amplifier board is quite a progression.

## The setup kept finding new ways to be annoying

Moving the decoding to Windows did not end the infrastructure work. The intermediate files were still huge. Writing them through a mount to the storage machine saved local scratch space, until the mount became unreliable.

The decoder itself became part of the work too. In my tape-decode fork, Codex investigated and wrote fixes for a bunch of issues where multithreaded decoding would jam up, with me overseeing the work. So this was not just asking an agent to run somebody else's program. It was also digging into why the program got stuck and changing the code. That does not mean every threading problem is gone, but those fixes are another part of how this setup kept moving forward.

A decoder can still be running while its output is no longer reaching the destination. A mount process can exist without being useful. Successfully launching a command tells me surprisingly little about whether the job is going to finish.

So there are checks for actual file growth, child-process logs, transfer lengths, and expected picture-field counts. There is also a chunked path: close a piece locally, transfer it, verify it, reclaim the space, and assemble the pieces in order afterward.

This is where the long journal earns its keep. Each tape has records of the current files, commands, attempts, and results. The procedures explain which machine does what and what counts as completing a stage. A later Codex conversation can inspect that state and continue instead of needing me to remember every detail.

I also use the shit out of goals in Codex for this. I give it an outcome to work toward, like getting a tape ingested, preserved, decoded, and reviewed, with the records updated. The goal says what I want finished; the repo's instructions, procedures, and journal give it the details needed to keep working toward that. There are a lot of intermediate steps in this setup, and I do not want “the decoder started” to be mistaken for the job being done.

The procedures change because the work changes. A failed mount leads to another check. A misleading preview changes the review instructions. Something that worked for a short test turns out to need a different approach for a whole tape.

I am pretty amazed Codex can deal with all of this. I can ask it to help capture, preserve, decode, review, export, and update the records across this weird setup. I still have to physically put the tape in the VCR. There are limits to the agent thing. 😅

And a process starting successfully is not successful preservation. Even finishing an encode does not establish that the source is safely retained, the audio is right, or the important sections were found. That is why the notes have become rather fussy about “done.”

## Cheaper does not mean cheap

People have been asking what this approach costs. I went back through my purchase emails, and even the partial breakdown is a reminder that an ordinary VCR does not make the whole project cheap.

| Item | Cost / replacement allowance |
| --- | ---: |
| Sony SLV-688HF VCR, including shipping and tax | $69.13 |
| Capture computer, salvaged from the junk pile at work | $0 |
| Samsung 980 1 TB NVMe scratch SSD, already on hand | $0 additional spending; about $300 replacement allowance based on the September 2026 listing below |
| Capture-PC boot SSD, PCIe NVMe adapter, BNC cable, tap lead, and SMA/BNC adapters, grouped in one shipment | $100.49 |
| S-Video-to-BNC splitter shipment | $8.77 |
| SMA DC-block shipment | $27.38 |
| Three PCIe capture cards for CXADC, including one dud | $90.02 |
| JLCPCB amplifier prototype batch: five PCBs, two assembled | About $140 |
| M5Stack Atom Lite ESP32 controller with built-in IR | $14 |
| 3D-printed stand for aiming the IR controller at the VCR | Cents |
| VCR power meter for transport-state monitoring | About $14 |
| Estimated additional spending for my setup | **About $464** |
| With that replacement allowance for the scratch SSD | **About $764** |

Apart from the scratch SSD's explicitly marked replacement allowance, these are historical purchase amounts for my setup, including things bought while experimenting, not a recommended shopping list. The three matching AliExpress card orders show $30.87, $31.48, and $27.67. One card was a dud, so I ended up buying three; that is my spending, not a requirement to buy three cards. The emails also carry a shipping-fee exclusion notice, so those amounts may not capture every charge.

The amplifier batch is about $140 from memory, pending the final invoice. I am counting the whole batch rather than dividing it into a misleadingly cheap single-board price. The Amazon shipment totals also differ from the initial order confirmation. With the $14 IR controller, roughly $14 power meter, and cents for the printed stand, call this roughly $464, with some accounting still to reconcile.

The scratch SSD was another thing I already had, not another free component somebody else can assume they will find. On September 13, 2026, a [Best Buy marketplace listing for a Samsung 990 EVO Plus 1 TB](https://www.bestbuy.com/product/samsung-990-evo-plus-ssd-1tb-pcie-gen-4x4--5x2-m-2-2280-speeds-up-to-7150-mb-s/J3ZYG2TVFS/sku/12161481) showed $299.74, sold and shipped by Merchant Expo LLC. That is the basis for the roughly $300 replacement allowance, before tax. It is a newer model than the Samsung 980 in my machine, and one listing rather than a cheapest-drive claim or a market average. This price comparison is a September snapshot.

The capture computer came out of the junk pile at work. It cost me nothing. Maybe call it a $100 used computer if you had to find something similar, but that is my guess, not a checked price. Including both that computer estimate and the scratch SSD allowance puts this partial budget around $864. The separate boot SSD and PCIe adapter I bought are already counted in the shipment above; the 1 TB scratch drive is not bundled into that amount.

The other computers already on hand, bulk storage, tools, label printer and its tape, smiley-face stickers, electricity, and AI usage are outside this subtotal too. Starting from nothing would be a different budget.

For comparison, the TBC-equipped decks I found on eBay while writing this were roughly $700–$1,600, depending on model and claimed condition. A [JVC HR-S9911U listing marked sold showed $900](https://www.ebay.com/itm/236911127606), a [seller-described rebuilt HR-S7600U was listed at about $1,150](https://www.ebay.com/itm/327171572501), and a [Panasonic AG-1980 listing describing capacitor replacement showed about $1,600](https://www.ebay.com/itm/396906605292). Those last two are advertised prices, not verified sale amounts, and this is a small sample rather than a market average.

My additional spending comes to around $464, helped by a free computer and a scratch SSD I already owned. Allowing for buying those two items brings this partial budget closer to $864, which overlaps the lower end of those TBC-deck examples. There is still a lot of assembly, troubleshooting, and existing equipment behind that number. The eBay prices are for the deck alone too, so neither side is a complete capture setup priced from scratch.

## Still growing

This started as a way to avoid paying for a fancy VCR. It has become a small preservation workflow, with considerably more DIY than the price comparison alone suggests.

I would not present it as a polished setup for somebody else to install. Parts work well, parts need babysitting, and the next tape can still expose another bad assumption.

I am keeping my own pipeline repo and tape collection private, but you do not need my exact repo to try this. I think this post should give you a starting point for making your own: write down what your machines do, keep a journal, and build up scripts and checks as you go. Steal whichever tricks here are useful, adapt them to your setup, and add the ones you figure out yourself. That is pretty much how mine grew.

But the payoff is that I can now put a tape in, start a new Codex thread in the repo, and give it an ingest goal.

There is a lot behind that little request, and it can still need my help when something goes wrong. But I no longer have to manually coordinate every command on every machine just to get through a tape. The scripts, checks, and long journal are what let a fresh thread pick up the job.

What amazes me is that Codex can coordinate the whole workflow across three computers, from capturing the tape through preserving, decoding, reviewing, and exporting it, with the records updated at the end. It also coordinates ASR for Cantonese, Mandarin, and English, helps translate the speech, finds home-video sections among the recorded TV, and points out interesting things it notices in the footage.

This sat on my someday pile for about two years. Now I can toss in a tape and ask Codex to take it through the whole process and help me discover what I have been keeping in those boxes. That feels magical.
