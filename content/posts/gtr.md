---
title: "Released Gargantuan Takeout Rocket"
date: 2022-04-16T21:04:00-08:00
tags:
  - google
  - takeout
  - azure
  - cloudflare
  - chrome
  - extensions
  - dataportability
  - backup
---

Finally released GTR or Gargantuan Takeout Rocket.

[GitHub repository][gtr-repo]

---

Gargantuan Takeout Rocket (GTR) is a toolkit of guides and software to help you take out your data from [Google Takeout][google-takeout] and put it somewhere else safe easily, periodically, and fast to make it easy to do the right thing of backing up your Google account and related services such as your YouTube account or Google Photos periodically.

---

It took a lot of time to research and to workaround the issues I found in [Azure][azure].

I also needed to take apart browser extensions and implement my own browser extension to facilitate the transfers.

[Cloudflare Workers][cloudflare-workers] was also used to work around issues with Azure's API.

All this combined, I was able to takeout 1.25TB of data in 3 minutes.

Now I'm showing it around on Twitter, Discord, Reddit, and more to users who have used VPSes to backup Google Takeout or have expressed dismay at the lack of options for users who are looking to store archives on the cheap. The response from people who've opted to stay on Google has been good!

There is also a project page with additional details [here][gtr-project-page].

[gtr-repo]: https://github.com/nelsonjchen/gtr
[google-takeout]: https://takeout.google.com/
[azure]: https://azure.microsoft.com/
[cloudflare-workers]: https://workers.cloudflare.com/
[gtr-project-page]: {{< relref "projects/gtr.md" >}}
