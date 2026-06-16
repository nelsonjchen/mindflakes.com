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

The short version is that I had built a small [Rust SMTP-to-Cloudflare REST API relay][smtp2cf-repo] in April. Today I noticed Cloudflare had shipped native SMTP submission.

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
- stay small enough to run on a cheap little VPS with an IPv4 address, because that is what I already had

Mostly, I wanted to avoid operating a real mail server for a personal domain.

## The temporary bridge

This is also one of those places where [Codex][openai-codex] changed what felt reasonable to do.

Before, I probably would have waited for Cloudflare's rollout or grudgingly set up a heavier mail stack. But as far as I could tell, there was no useful public date for SMTP support. So I was impatient and made the bridge I wanted.

My first `smtp2cf` commit was April 18, 2026. I had the repo cleaned up and documented by April 20. It was not a forever project. It was a practical way to bridge the gap until Cloudflare shipped the official SMTP path.

Cloudflare's docs now have a changelog entry dated June 8, 2026: [Authenticated SMTP submission now available in beta][cloudflare-smtp-changelog]. That means the relay did its job and can leave the live path.

## The new setup

Cloudflare's [SMTP docs][cloudflare-smtp] and Gmail's [Send mail as docs][gmail-send-mail-as] line up pretty directly:

- host: `smtp.mx.cloudflare.net`
- port: `465`
- security: implicit TLS
- username: `api_token`
- password: a Cloudflare API token with Email Sending access

I generated a token, changed the Gmail Send mail as SMTP settings, sent a test message, and watched it go through Cloudflare without my relay in the middle.

That is the best kind of infrastructure migration: delete a moving part and then immediately wonder why you ever had to own it.

## The now historical Rust thing

The repo is still here:

https://github.com/nelsonjchen/smtp-2-cf-email-service

It is now mostly historical for me.

I still like a few parts of it. The durable spool is the right instinct. Returning `250 OK` before the message is safely written somewhere is how you earn a future debugging session. The Gmail header cleanup is gross but also a useful reminder that "raw MIME" does not always mean "whatever one mail system happened to emit is acceptable to another one."

## My past email setups

This domain has had a few outgoing mail setups over the years.

Way back, I used [DreamHost][dreamhost-email] for Gmail's [Send mail as][gmail-send-mail-as] SMTP server. That mostly worked, but it was still another provider in the path.

Later, I used [Zoho Mail][zoho-mail] as a sort of middleman mailbox. Gmail would send as the domain through Zoho, and Zoho would receive mail and forward it onward. I was not happy with either side of that. Incoming forwarding would sometimes fail to take, delivery rates sucked on Zoho, sending email out through Zoho SMTP was a shitshow, and traffic from Zoho to Gmail was a shitshow too.

The most annoying symptom was the delivery report email. I would get an email with a ZIP file of delivery-rate data, and the vibe was never "everything is fine." It was more like "congratulations, your mail technically attempted to exist."

Then came `smtp2cf`, the Rust relay in this post. That got me away from Zoho and let Gmail hand mail to my own tiny SMTP bridge, which then sent through Cloudflare's raw MIME API. It was better because it was mine and inspectable, but it was still a moving part I had to run.

This Cloudflare SMTP setup is the first version that feels like the shape I wanted all along: Gmail UI, personal domain, Cloudflare for the domain and mail plumbing, and no mailbox vendor sitting in the middle.

Anyway, that is the trip report. I am happy this one is boring now.

[cloudflare-email-service]: https://developers.cloudflare.com/email-service/get-started/send-emails/
[cloudflare-email-routing]: https://blog.cloudflare.com/introducing-email-routing/
[cloudflare-private-beta]: https://blog.cloudflare.com/email-service/
[cloudflare-public-beta]: https://blog.cloudflare.com/email-for-agents/
[cloudflare-send-raw]: https://developers.cloudflare.com/api/resources/email_sending/methods/send_raw
[cloudflare-smtp]: https://developers.cloudflare.com/email-service/api/send-emails/smtp/
[cloudflare-smtp-changelog]: https://developers.cloudflare.com/changelog/post/2026-06-08-smtp-submission/
[dreamhost-email]: https://www.dreamhost.com/hosting/email/
[gmail-send-mail-as]: https://support.google.com/mail/answer/22370
[google-workspace-pricing]: https://workspace.google.com/pricing.html
[openai-codex]: https://openai.com/codex/
[smtp2cf-repo]: https://github.com/nelsonjchen/smtp-2-cf-email-service
[zoho-mail]: https://www.zoho.com/mail/
