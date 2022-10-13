---
title: "Datasette-lite sqlite-httpvfs experiment POC with the California Unclaimed Property database"
date: 2022-10-11T00:00:00Z
tags:
  - datasette
  - datasette-lite
  - sqlite-httpvfs
  - experiment
  - sqlite
  - cloudflare
  - r2
---

![screenshot](https://user-images.githubusercontent.com/5363/195697437-2331acfb-fedd-478f-8596-28d6a1809339.png)

There's a web browser version of datasette called datasette-lite which runs on Python ported to WASM with Pyodide which can load SQLite databases.
I grafted the enhanced lazyFile implementation from emscripten and then from this implementation to datasette-lite relatively recently as a curious test. Threw in a 18GB CSV from CA's unclaimed property records here

https://www.sco.ca.gov/upd_download_property_records.html

into a FTS5 Sqlite Database which came out to about 28GB after processing:

POC, non-merging Log/Draft PR for the hack:

https://github.com/simonw/datasette-lite/pull/49

You can run queries through to datasette-lite if you URL hack into it and just get to the query dialog, browsing is kind of a dud at the moment since datasette runs a count(*) which downloads everything.

[Elon Musk's CA Unclaimed Property](https://datasette-lite-lab.mindflakes.com/index.html?url=https://datasette-lite-lab.mindflakes.com/sdb/2022-10-02_93eff57de3573985_ca_unclaimed_property.sqlite#/2022-10-02_93eff57de3573985_ca_unclaimed_property?sql=SELECT+*+FROM+records+WHERE+records.owner_name+MATCH+%22Elon+Musk%22+ORDER+BY+CAST%28CURRENT_CASH_BALANCE+AS+FLOAT%29++DESC%3B)

Still, not bad for a $0.42/mo hostable cached CDN'd read-only database. It's on Cloudflare R2, so there's no BW costs.

Celebrity gawking is one thing, but the real, useful thing that this can do is search by address. If you aren't sure of the names, such as people having multiple names or nicknames, you can search by address and get a list of properties at a location. This is one thing that the [California Unclaimed Property site can't do](https://ucpi.sco.ca.gov/en/Property/SearchIndex).

I am thinking of making this more proper when R2 introduces lifecycle rules to delete old dumps. I could automate the process of dumping with GitHub Actions but I would like R2 to handle cleanup.
