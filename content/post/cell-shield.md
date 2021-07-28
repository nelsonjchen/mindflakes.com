---
title: "Cell Shield"
date: 2021-07-28T00:00:00Z
tags:
  - google_spreadsheets
  - markdown
  - html
  - golang
  - cloud_run
  - bigquery
  - gcp
---

I built https://cellshield.info to scratch an itch. Why can't I embed a
spreadsheet cell?

[
![](https://shields.io/endpoint?url=https%3A%2F%2Fcellshield.info%2Fgs%3FspreadSheetId%3D1MkUQwY33RY0gJwJtls48mjISBzPgd8noanZee-omezs%26cellRange%3DC3)
](https://docs.google.com/spreadsheets/d/1MkUQwY33RY0gJwJtls48mjISBzPgd8noanZee-omezs/edit#gid=0&range=SAShowPost)

You can click on that badge and see the sheet that is the data backing the
badge.

It is useful for embedding a cell from an informal database that is a Google
Spreadsheet atop of some README, wiki, forum post, or web page. Spreadsheets are
still how many projects are managed and this can be used to embed a small
preview of important or attention grabbing data.

It is a simple Go server that uses Google's API to output JSON that
https://shields.io can read. Mix it in with some simple VueJS generator
front-end to take public spreadsheet URLS and tada.

I picked to base my implementation on shields.io's as they make really pretty
badges and have a lot of nice documentation and options.

I run it on Cloud Run to keep the costs low and availability high. It also
utilizes Google's implementation of authorization to get access to the Sheets API.

The badge generator web UI can also make BBCode for embedding (along with modern
Markdown and so on).

I've already used it for
[this](https://github.com/commaai/openpilot/wiki/Toyota-Lexus#2021-toyota-ecu-security-key-support-new-steering_lka--more)
and [that](https://github.com/commaai/comma10k#comma10k). The former outputs a
running bounty total and the latter is community annotation progress.

A major Game Boy development contest is using it labels to display the prize
pool amount: https://itch.io/jam/gbcompo21

## Why I built this?

I built it for the [Comma10K](https://github.com/commaai/comma10k#comma10k)
annotation project. I contributed the original badge which attempted to use a
script that was used in the repo to analyze the completion progress.
Unfortunately, this kept on diverging from the actual progress because the human
elements kept changing the structure and organization and I had the badge
removed. The only truth is what was on the spreadsheet.

[![Completion Status](https://img.shields.io/endpoint?color=%2328B224&label=Completion%20Status&url=https%3A%2F%2Fcellshield.info%2Fgs%3FspreadSheetId%3D1ZKqku0cAyWY0ELY5L2qsKYYYA2AMGbgAn4p53uoT3v8%26cellRange%3Di1)](https://docs.google.com/spreadsheets/d/1ZKqku0cAyWY0ELY5L2qsKYYYA2AMGbgAn4p53uoT3v8/edit#gid=0)

## Popularity

Compared to my other projects, I don't think the popularity will be too good for
this project though. I think it might have a chance to spread via word of mouth
but it's not everyday that a large public collaborative project is born with a
Google Spreadsheet managing it. The name isn't that great either as it probably
conflicts with anti-wireless hysteria. Also, it's not like there's an agreed
upon community of Google Spreadsheet users to share this knowledge with.

## Maintenance

Eh, it's on Cloud Run. The cost to keep it running is low as it sometimes isn't
even running and the Sheets V4 API it uses was recently declared
"enterprise-stable". It'll be super easy to maintain and cheap to run.
