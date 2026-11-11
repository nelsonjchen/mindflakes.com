---
title: "Milkdo: the grocery bot Yrenia and I keep fixing"
date: 2026-11-08
draft: false
slug: "milkdo-grocery-bot"
tags:
  - telegram
  - todoist
  - cloudflare
  - automation
  - github-copilot
  - codex
  - ai-assisted
---

[Yrenia](https://yrenia.com/) and I use a Telegram bot called [Milkdo][repo] to maintain our Todoist grocery list. We can send a message or a voice note in our group instead of opening Todoist to enter each item. It's a small tool we actually use, and we keep fixing the parts that annoy us.

I first had a go at this myself in 2024, cobbling the code together by hand with some help from GitHub Copilot autocomplete. Much later, I brought it forward with OpenAI Codex doing substantial refactoring, bug investigation, implementation and tests under my direction. Codex also helped draft this post. This is a codebase that went from lightly AI-assisted to heavily agent-refactored over more than two years of use and maintenance.

I don't think of Milkdo as a “claw.” It's our grocery bot, with a specific list and a small set of operations. The point is to make maintaining that list less annoying for the two of us.

## My first attempt, back in 2024

The first commit was on May 8, 2024. The codebase started as Dialoh, a bot for conversations with Ukrainian-speaking friends. I wanted to deploy it on Cloudflare and use its serverless services with Telegram. On May 9 I was working through webhooks, voice messages and transcription; by May 12, there was a working Durable Object integration to keep conversation history.

That was the stage where I was mostly writing the code myself, with Copilot suggesting completions. The bot itself already used a language model to interpret messages. What changed later was how much of the coding I handed over to an agent.

On August 4, 2024, the first grocery implementation could add items to Todoist, with a due date and an emoji in the item's name. The Telegram and Cloudflare plumbing from the conversation bot gave me somewhere to build it. It was a limited first version, but it addressed something Yrenia and I wanted to do.

This then became a codebase we kept around and maintained. On May 8, 2025, the commits show repairs to Todoist request handling and project/section IDs, along with API tests. There were dependency and prompt updates along the way too. The original attempt didn't turn into the current version in one sitting.

## Coming back to it with Codex

By September 20, 2026, I was using Codex to work through a much larger round of changes. It fixed grocery-message processing, Pacific-time dates and error reporting, moved model/tool exchanges to the Responses API, and added deletion, list reading, editing and duplicate consolidation.

This involved substantial refactoring of the code I had started with. Date handling, shopping-list operations, tool definitions and model-response history gained separate modules, with tests around the behavior we needed. I could direct the work around things that annoyed us in use, and have Codex investigate and change the implementation.

The deployment stayed built around Telegram and Cloudflare Workers. A Durable Object keeps each chat's conversation history, so a follow-up can refer to something discussed earlier. Voice messages go through transcription first. Yrenia and I could keep using the group while the code behind it changed.

## More than adding milk

Adding milk is useful. Eventually you also need to remove it, change it to oat milk, move it to tomorrow, or figure out why there are three entries for the same thing. The September work gave Milkdo those operations and dealt with the awkward follow-ups.

“Move the milk to tomorrow at 5 PM” should update an existing item. If there are two active milk entries, the bot should ask which one. The selected item's identity needs to survive the follow-up, even if one name has a milk emoji and the other doesn't.

Reading the list matters too. An answer about what's on it needs to come from a fresh Todoist result. The chat history can help interpret the request, but it can also describe an item that has since been bought or removed.

Dates produced another irritating problem. The application now supplies the current Pacific date and time explicitly and resolves dates in that timezone. UTC midnight can arrive while it is still yesterday here. A grocery bot shouldn't move tomorrow's shopping an extra day into the future because its server has a different clock.

## Fixing it without losing the groceries

Consolidating duplicates takes more care than deleting an extra row. The operation first reads the selected items, checks that they still exist, and updates the item being kept before removing the others. Notes are preserved unless a replacement was requested. Conflicting schedules require a date decision, and recurring items currently cannot be consolidated.

One case the refactor had to account for was deletion failing halfway through a merge. The bot now reports which items still need checking and that the kept item has already been updated. Retrying the whole merge blindly could add the quantities again. Codex added tests around that partial failure, as well as duplicate selection, field preservation and the timezone boundary.

The model interprets the conversation; the application gives it concrete operations and checks their results. The order of the actual writes matters when the bot is changing or deleting things on a list we depend on.

This is a personal tool. The [public repository][repo] has hardcoded user and project restrictions, and running it requires service credentials and hosting. It should give you a starting point if you want something similar.

I like that something I had a go at building a couple of years ago is still useful to us. The code has changed a lot, and so has the way I work on it: from writing it with autocomplete to directing agents through larger refactors. Yrenia and I still have the same basic need: put the groceries on the list and keep it straight. When something gets in the way, we keep fixing it.

[repo]: https://github.com/nelsonjchen/milkdo-bot
