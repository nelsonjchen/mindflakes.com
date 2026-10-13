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
  - ai-assisted
  - codex
---

I just finished moving my Gmail "Send mail as" setup to [Cloudflare Email Service's native SMTP submission][cloudflare-smtp]. I can send email from my mindflakes.com address.

I built the SMTP relay described here with the assistance of Codex. I supplied the idea and directed the work; Codex did the technical investigation and implementation. Codex also drafted this post.

**Update, October 12, 2026: Google is removing Gmail's "Send as" support for third-party addresses in January 2027.** [Google's announcement][gmail-send-as-removal] says existing setups continue during the transition, but new configurations may be restricted. Gmail-to-Gmail aliases and Google Workspace "Send as" are exempt. Incoming forwarding is unaffected. The sending setup described here now has an end date. Darn, I was looking forward to this for years. The rest of this post describes what worked in June.

Back in April, I had built a small [Rust SMTP-to-Cloudflare REST API relay][smtp2cf-repo]. Today I noticed Cloudflare had shipped native SMTP submission.

Part of the appeal here is minimizing vendors. Cloudflare already hosts the domain, handles incoming mail through [Email Routing][cloudflare-email-routing], and now can handle outgoing mail through [Email Service][cloudflare-email-service] too. For a personal domain, that can be cheap or free, and it is a nice alternative to paying for [Google Workspace][google-workspace-pricing] when a personal Gmail account is otherwise fine.

## The April hack

In April, I saw Cloudflare's [public beta announcement][cloudflare-public-beta] for Email Service and got excited about using Cloudflare for both inbound and outbound personal-domain mail.

Then I got sad. I wanted Gmail to send mail for one of my domains through [Cloudflare Email Service][cloudflare-email-service], but I did not see a native SMTP submission path I could just point Gmail at.

Gmail's [Send mail as][gmail-send-mail-as] feature wants a normal authenticated SMTP server. You give it a hostname, a port, a username, and a password. Gmail talks SMTP. It does not want to be handed a REST API endpoint.

At the time, Cloudflare's available sending interface was the raw MIME REST API. Cloudflare had teased SMTP during the private beta, but it was not available in the public beta I was using.

So I had Codex build `smtp2cf` under my direction.

It was intentionally narrow:

- accept authenticated SMTP from Gmail
- write the raw MIME message to disk before returning `250 OK`
- forward that raw MIME to Cloudflare's [`send_raw` API][cloudflare-send-raw]
- retry transient failures
- move permanent failures to a dead-letter directory
- stay small enough to run on a cheap little VPS with an IPv4 address, because that is what I already had

Mostly, I wanted to avoid operating a real mail server for a personal domain.

## The temporary bridge

This is also one of those places where [Codex][openai-codex] changed what felt reasonable to do.

Before, I probably would have waited for Cloudflare's rollout or grudgingly set up a heavier mail stack. But as far as I could tell, there was no useful public date for SMTP support. So I was impatient and directed Codex to build the bridge I wanted.

My first `smtp2cf` commit was April 18, 2026. By April 20, Codex had cleaned up and documented the repo under my direction. I only needed the relay until Cloudflare shipped SMTP support.

Cloudflare's docs now have a changelog entry dated June 8, 2026: [Authenticated SMTP submission now available in beta][cloudflare-smtp-changelog]. That meant I could stop running the relay.

## The new setup

Cloudflare's [SMTP docs][cloudflare-smtp] and Gmail's [Send mail as docs][gmail-send-mail-as] line up pretty directly:

- host: `smtp.mx.cloudflare.net`
- port: `465`
- security: implicit TLS
- username: `api_token`
- password: a Cloudflare API token with Email Sending access

I generated a token, changed the Gmail Send mail as SMTP settings, sent a test message, and watched it go through Cloudflare without my relay in the middle.

It was a relief to have one fewer service to run.

## The now historical Rust thing

The repo is still here:

https://github.com/nelsonjchen/smtp-2-cf-email-service

It is now mostly historical for me.

I still like a few parts of it. The durable spool is the right instinct. Returning `250 OK` before the message is safely written somewhere is how you earn a future debugging session. The Gmail header cleanup is gross but also a useful reminder that "raw MIME" does not always mean "whatever one mail system happened to emit is acceptable to another one."

## My past email setups

This domain has had a few outgoing mail setups over the years.

Way back, I used [DreamHost][dreamhost-email] for Gmail's [Send mail as][gmail-send-mail-as] SMTP server. That mostly worked, but it was still another provider in the path.

Later, I used [Zoho Mail][zoho-mail] as a sort of middleman mailbox. Gmail would send as the domain through Zoho, and Zoho would receive mail and forward it onward. I was not happy with either side of that. Incoming forwarding would sometimes fail to take, delivery rates sucked on Zoho, sending email out through Zoho SMTP was a shitshow, and traffic from Zoho to Gmail was a shitshow too.

The most annoying symptom was the delivery report email. I would get an email with a ZIP file of delivery-rate data, and those reports did not inspire confidence.

Then came `smtp2cf`, the Rust relay in this post. That got me away from Zoho and let Gmail hand mail to my own tiny SMTP bridge, which then sent through Cloudflare's raw MIME API. It was better because it was mine and inspectable, but it was still a moving part I had to run.

This Cloudflare SMTP setup finally gave me what I wanted: Gmail's interface, my personal domain, and Cloudflare handling the domain and mail without another mailbox provider in between.

That was the June trip report. I was happy to have the setup working.

[cloudflare-email-service]: https://developers.cloudflare.com/email-service/get-started/send-emails/
[cloudflare-email-routing]: https://blog.cloudflare.com/introducing-email-routing/
[cloudflare-private-beta]: https://blog.cloudflare.com/email-service/
[cloudflare-public-beta]: https://blog.cloudflare.com/email-for-agents/
[cloudflare-send-raw]: https://developers.cloudflare.com/api/resources/email_sending/methods/send_raw
[cloudflare-smtp]: https://developers.cloudflare.com/email-service/api/send-emails/smtp/
[cloudflare-smtp-changelog]: https://developers.cloudflare.com/changelog/post/2026-06-08-smtp-submission/
[dreamhost-email]: https://www.dreamhost.com/hosting/email/
[gmail-send-mail-as]: https://support.google.com/mail/answer/22370
[gmail-send-as-removal]: https://support.google.com/mail/answer/17101213?hl=en
[google-workspace-pricing]: https://workspace.google.com/pricing.html
[openai-codex]: https://openai.com/codex/
[smtp2cf-repo]: https://github.com/nelsonjchen/smtp-2-cf-email-service
[zoho-mail]: https://www.zoho.com/mail/
