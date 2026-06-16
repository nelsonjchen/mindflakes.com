---
title: "codex-status-json"
date: 2026-06-16T15:03:00-07:00
slug: "codex-status-json"
tags:
  - codex
  - rust
  - cli
  - json
  - automation
---

I made a small [Rust CLI that prints Codex status as JSON][codex-status-repo].

The short version is that I wanted Codex conversations to know my account, model, and quota state without opening the interactive TUI and reading `/status` like a person.

This is mostly useful with Codex goals. I am on the [Pro 20x plan][codex-pricing], which is the $200/month tier, so sometimes I really do have quota to burn. If I can read the remaining quota first, I can make a goal with an actual target instead of guessing how ambitious I should be.

## The missing command

Codex has `/status` in the interactive CLI. It shows useful stuff: account, model, context, writable roots, and rate limits.

That is fine when I am sitting in the TUI. It is less fine when I want another Codex conversation to ask the same question.

There is an upstream issue asking for this exact kind of thing: [a non-interactive JSON/headless replacement for `/status`][codex-status-issue]. The issue is still open as I write this. There is also `codex doctor --json`, which is useful, but that is a diagnostic report. It is not the same as "tell me my account/model/quota status right now."

So I made `codex-status-json`.

## The first version was worse

The first version did what you would expect from a tool born out of impatience: it opened Codex in a PTY, sent `/status`, waited for the panel to settle, captured the terminal output, stripped ANSI escapes, and parsed the text.

It worked. It was also clearly a bad long-term place to live.

Terminal scraping is annoying because every little UI change can break you. The parser has to care about box drawing, timing, update notices, trust prompts, terminal width, and whatever else the TUI happens to render that day.

Still, it was useful. It gave me JSON like this:

```json
{
  "schema_version": 1,
  "account": {
    "email": "user@example.com",
    "plan": "Pro",
    "raw": "user@example.com (Pro)"
  },
  "model": {
    "name": "gpt-5.5",
    "details": "reasoning high",
    "raw": "gpt-5.5 (reasoning high)"
  },
  "buckets": [
    {
      "name": "default",
      "limits": [
        {
          "kind": "5h",
          "remaining_percent": 99,
          "resets": "14:44",
          "reset_at_unix": 1780350290
        },
        {
          "kind": "weekly",
          "remaining_percent": 70,
          "resets": "08:23 on 7 Jun",
          "reset_at_unix": 1780845815
        }
      ]
    }
  ]
}
```

That was enough to unblock the thing I wanted.

## The less cursed version

A few days later, I switched the default backend to Codex's [app-server][codex-app-server-docs].

That is a much better source than scraping the TUI. The tool starts:

```sh
codex app-server --listen stdio://
```

Then it sends JSON-RPC calls for:

- `account/read`
- `account/rateLimits/read`
- `config/read`

That gives it the same kind of account, model, and quota information without pretending a terminal status panel is an API.

Important caveat: Codex app-server is documented, but it is also described as experimental and may change. So this is still not a supported `codex status --json`. It is just a more sensible workaround than screen scraping.

The PTY backend is still there as a fallback:

```sh
codex-status-json --backend pty
```

But the default is now:

```sh
codex-status-json --backend app-server
```

## This is a bridge

I do not expect this to be the forever answer.

Codex's usage and billing surface is still moving. OpenAI has already introduced things like [banked rate-limit resets][codex-pricing], so the status shape is not just "two counters and call it done." There are plans, windows, reset times, secondary buckets, and whatever else comes next.

So I can see why OpenAI might not want to commit to a stable status JSON contract yet. That is fine. I still wanted something local that worked now.

This is the same kind of bridge as a lot of these small tools: use the available pieces, make the annoying gap tolerable, and be happy if the official thing eventually replaces it.

## The repo

The project is here:

https://github.com/nelsonjchen/codex-status-command

Install it locally with:

```sh
cargo install --path .
```

The README has the options and limitations. The tests include a captured `/status` fixture so the text parser can at least fail loudly when Codex changes its display.

I would still rather have an official `codex status --json`. Until then, this is small, local, and useful enough.

[codex-app-server-docs]: https://developers.openai.com/codex/app-server
[codex-pricing]: https://developers.openai.com/codex/pricing
[codex-status-issue]: https://github.com/openai/codex/issues/10233
[codex-status-repo]: https://github.com/nelsonjchen/codex-status-command
