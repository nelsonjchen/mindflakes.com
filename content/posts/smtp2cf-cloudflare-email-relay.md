---
title: "Moving Gmail Send mail as to Cloudflare SMTP"
date: 2026-06-16T14:13:00-07:00
slug: "gmail-send-mail-as-cloudflare-smtp"
tags:
  - rust
  - smtp
  - cloudflare
  - gmail
  - email
---

I just finished moving my Gmail "Send mail as" setup completely to [Cloudflare Email Service's native SMTP submission][cloudflare-smtp]. I can send out emails under my mindflakes.com domain.

The short version is that I had built a small [Rust SMTP-to-Cloudflare REST API relay][smtp2cf-repo] in April. Today I noticed Cloudflare now has the thing I wanted in the first place.

Part of the appeal here is minimizing vendors. Cloudflare already hosts the domain, handles incoming mail through [Email Routing][cloudflare-email-routing], and now can handle outgoing mail through [Email Service][cloudflare-email-service] too. For a personal domain, that can be cheap or free, and it is a nice alternative to paying for [Google Workspace][google-workspace-pricing] when a personal Gmail account is otherwise fine.

## The April hack

In April, I saw Cloudflare's [public beta announcement][cloudflare-public-beta] for Email Service and got excited about using Cloudflare for both inbound and outbound personal-domain mail.

Then I got sad. I wanted Gmail to send mail for one of my domains through [Cloudflare Email Service][cloudflare-email-service], but I did not see a native SMTP submission path I could just point Gmail at.

Gmail's [Send mail as][gmail-send-mail-as] feature wants a normal authenticated SMTP server. You give it a hostname, a port, a username, and a password. Gmail talks SMTP. It does not want to be handed a REST API endpoint.

At the time, the useful Cloudflare sending shape I had in front of me was the raw MIME REST API. Cloudflare had teased SMTP in the earlier private-beta era, but the public thing I could actually use was not SMTP.

So I made `smtp2cf`.

It was intentionally narrow:

- accept authenticated SMTP from Gmail
- write the raw MIME message to disk before returning `250 OK`
- forward that raw MIME to Cloudflare's [`send_raw` API][cloudflare-send-raw]
- retry transient failures
- move permanent failures to a dead-letter directory
- stay small enough to run on a cheap little VPS with an IPv4 address

Mostly, I wanted to avoid operating a real mail server for a personal domain.

Maybe an IPv6-only VPS would have worked too. I did not test that. The little IPv4 box was already there, and the whole point was to make this boring.

## The temporary bridge

This is also one of those places where [Codex][openai-codex] changed what felt reasonable to do.

Before, I probably would have waited for Cloudflare's rollout or grudgingly set up a heavier mail stack. But as far as I could tell, there was no useful public date for SMTP support. So I was impatient and made the bridge I wanted.

My first `smtp2cf` commit was April 18, 2026. I had the repo cleaned up and documented by April 20. It was not a forever project. It was a practical way to bridge the gap until Cloudflare shipped the official SMTP path.

Cloudflare's docs now have a changelog entry dated June 8, 2026: [Authenticated SMTP submission now available in beta][cloudflare-smtp-changelog]. Good. That is exactly what I wanted. I am happy to delete my little bridge when the real road shows up.

## The new setup

Cloudflare's [SMTP docs][cloudflare-smtp] and Gmail's [Send mail as docs][gmail-send-mail-as] line up pretty directly:

- host: `smtp.mx.cloudflare.net`
- port: `465`
- security: implicit TLS
- username: `api_token`
- password: a Cloudflare API token with Email Sending access

That is exactly the shape Gmail wants.

I generated a token, changed the Gmail Send mail as SMTP settings, sent a test message, and watched it go through Cloudflare without my relay in the middle.

That is the best kind of infrastructure migration: delete a moving part and then immediately wonder why you ever had to own it.

## The historical Rust thing

The repo is still here:

https://github.com/nelsonjchen/smtp-2-cf-email-service

It is now mostly historical for me.

I still like a few parts of it. The durable spool is the right instinct. Returning `250 OK` before the message is safely written somewhere is how you earn a future debugging session. The Gmail header cleanup is gross but also a useful reminder that "raw MIME" does not always mean "whatever one mail system happened to emit is acceptable to another one."

But I do not need to run it anymore. Cloudflare now speaks the protocol Gmail wanted all along.

Anyway, that is the trip report. I built the bridge, Cloudflare shipped the road, and I am happy to delete my bridge from the live path.

[cloudflare-email-service]: https://developers.cloudflare.com/email-service/get-started/send-emails/
[cloudflare-email-routing]: https://blog.cloudflare.com/introducing-email-routing/
[cloudflare-private-beta]: https://blog.cloudflare.com/email-service/
[cloudflare-public-beta]: https://blog.cloudflare.com/email-for-agents/
[cloudflare-send-raw]: https://developers.cloudflare.com/api/resources/email_sending/methods/send_raw
[cloudflare-smtp]: https://developers.cloudflare.com/email-service/api/send-emails/smtp/
[cloudflare-smtp-changelog]: https://developers.cloudflare.com/changelog/post/2026-06-08-smtp-submission/
[gmail-send-mail-as]: https://support.google.com/mail/answer/22370
[google-workspace-pricing]: https://workspace.google.com/pricing.html
[openai-codex]: https://openai.com/codex/
[smtp2cf-repo]: https://github.com/nelsonjchen/smtp-2-cf-email-service
