---
title: "Keeping comma hardware repair answers somewhere findable"
date: 2026-09-06T10:34:51-07:00
draft: false
slug: "comma-repair-answers"
tags:
  - comma-ai
  - openpilot
  - hardware
  - documentation
  - ai-assisted
  - codex
---

One of the things I keep working on is the [unofficial comma hardware documentation][docs]. Somebody has a broken device, people help investigate, and eventually there is an answer somewhere in Discord.

I wrote the early documentation by hand. More recently, I've been using Codex to crawl through Discord discussions for updates, write additions, and straighten out existing material, under my direction. The repairs and reported results come from the community members credited in the docs. Codex also drafted this blog post.

Then somebody else has the same problem.

Discord is an annoying place for the answer to live. Finding the right conversation is work, the useful detail might be several messages away from the original question, and a person searching the web may never find it at all.

I hate walled gardens, and Discord is a bit of one. It is the same frustration that led me to build [GitHub Wiki SEE]({{< relref "github-wiki-see.md" >}}) back in 2021: people were putting useful information into GitHub wikis, but GitHub was blocking search engines from indexing it. I built a mirror to make those pages findable. Getting repair knowledge out of Discord and onto the public web comes from the same impulse.

So I keep trying to get those cases into a public document that search engines and AI assistants can read. Especially AI assistants, because people already ask them how to fix things. I want the actual repair knowledge to be available when somebody asks about their broken comma.

## The useful part is the follow-up

The document is organized around cases: what the person sees, what might be wrong, and what has helped other people with that particular problem. It has grown quite a bit. I do not expect anybody to sit down and read the whole thing in order.

Recent additions and updates include more fuse repair reports, a fastboot-only recovery case, and another cable gotcha. These are the small details that can save somebody a lot of time if they are findable at the right moment.

Fuse cases are a good example of why the reports matter. A suspected fault and a confirmed repair are different pieces of information. Somebody coming back with “that fixed it” makes the original discussion much more useful. An unsuccessful repair is worth recording too, especially if the symptoms seemed to fit.

The repair work belongs to the community members doing it and reporting their results. My contribution here is collecting the cases, keeping the references, and updating the guidance as more information comes in.

## Similar-looking problems need different answers

The fastboot-only case covers a specific recovery situation on the comma three and comma 3X. A device can still show up in fastboot while being unable to enter the EDL mode used by the current flasher. Seeing a USB device on the computer does not establish that the flasher can talk to it in the mode it needs.

That distinction needs to stay attached to the recovery notes. Otherwise a workaround for a particular failure starts looking like a general recommendation for everybody.

The comma four cable case is another good one. The included OBD-C cable does its intended installation job, but lacks the USB data lines needed for computer flashing. That is a pretty frustrating detail to discover while you are already trying to recover something.

Neither case needs a second set of instructions here that will drift out of date. The maintained document has the details and references.

## Constant little updates

This work is mostly incremental. Another symptom, another result, a clearer explanation of which device a case applies to. Sometimes the useful update is just making it harder to confuse two similar situations.

I like having a place where those answers can accumulate. Whether somebody finds it through a search engine or asks an AI to help, I want them to end up at the relevant case, with the references and pictures available to check. The answer should have somewhere to point back to.

The [docs are here][docs]. If you try a remedy from them, please report back whether it worked. That follow-up is part of what makes the next version of the document better.

I might need these docs myself soon. My own comma 3X did not boot when I tried powering it over USB. I have not figured out why yet, so I may get to find out how useful my own documentation is from the other side.

[docs]: https://github.com/ophwug/docs
