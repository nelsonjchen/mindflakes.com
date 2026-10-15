---
title: "A daily repair job for the filament tracker"
date: 2026-10-15
draft: true
slug: "bambu-stock-watcher-daily-repairs"
tags:
  - bambu
  - filament
  - automation
  - codex
  - ai-assisted
---

I added a daily Codex job to check [the filament tracker]({{< relref "bambu-stock-watcher.md" >}}). When it finds a problem it can diagnose clearly, it can fix the code, validate the change, deploy it, and check the live site again.

I set the requirements and gave it permission to make scoped repairs. Codex does the investigation, implementation, and deployment. Codex also drafted this post from the recorded runs and my direction.

The stock scraper already runs hourly. This is a separate maintenance check: are the pages fresh, are products missing, do the history charts still connect to the current inventory, and do the bulk-sale markers agree with Bambu's promotion data? It checks all eight regional pages and opens charts in a browser too.

Two runs show why I wanted this.

## Whitespace broke 41 charts

On September 18, the daily check found 41 Japanese variants whose current rows no longer matched their history keys. The storefront's product categories had trailing or duplicate whitespace. The inventory was there, but the chart lookup was broken.

Codex changed the report generator to normalize Unicode and whitespace in the history keys and put the resolved key on each row. It committed and pushed [the fix][whitespace-fix], deployed through the project's deployment script, and ran the production job.

The live check found no current variants missing history. It also opened a repaired Japanese PLA Silk+ chart and a Global Ash Gray chart, and checked that the regional pages were fresh and below the 2 MB size limit.

## Blue became Reflex Blue

On October 12, the check found another split in Japan's history. PETG Basic Blue had become Reflex Blue, but it still had color code `30603` and the same packaging. Matching by the displayed name made one product look like two separate histories.

Codex added a narrow alias for that rename, [committed and pushed it][rename-fix], validated it locally, and deployed it. The stored history records were preserved. Afterward, the spool and refill charts each had one continuous 723-point series spanning September 12 through October 12. Codex checked the live Japan and Global charts as well.

These are small fixes, but they are the sort of maintenance I would otherwise have to notice and sit down to investigate. The daily job gives Codex a reason to look before I happen to open the affected chart.

The checks still have limits. Bambu's regional promotion pages sometimes redirect to the US page, so Codex reports that it cannot independently verify each region's bulk-sale rules. If it cannot establish a clear fix, the job is instructed to report the problem for me to follow up.

[whitespace-fix]: https://github.com/nelsonjchen/bambu-us-stock-watcher/commit/62cc37132b9c9b4795cdeee924239631f6312c02
[rename-fix]: https://github.com/nelsonjchen/bambu-us-stock-watcher/commit/ea299e66d024d128da46b798d921e4ed27d9931c
