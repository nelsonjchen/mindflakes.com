---
date: 2019-05-30T00:00:00-08:00
tags:
- qt
- azure_pipelines
- barrier
title: "Barrier CI System WIP"
---

This was one of those adventures where you find a problem and it snowballs from there.

I wanted to use [barrier][barrier] which is a tool for sharing mouses between computers. It is a fork of Synergy, which had gone commercial.

So, I noticed on their [GitHub repo][barrier] that the builds for various platforms was inconsistent. OK, maybe I can help fix their CI systems. 

More worringly, there was some Appveyor CI system configured. It was not configured with the `appveyor.yml` method and the 

My current CI system of choice for open source projects is [Microsoft's Azure Pipelines][ap]. For open source projects on GitHub, they'll provide 10 parallel jobs of *Mac*, Linux, and *Windows*. *Mac* and *Windows* usually only show up as premium options or are severely rate-limited. As far as I can tell, whatever cluster Microsoft has created for their service, it's really not limited by capacity. 

[The good news is that they were fans of it and I was able to move them.][barrier_move_azp] It's still a work in progress. The work done here has draw in some general "improve the ecosystem" enhancements which I will elaborate on in future posts.

[barrier]: https://github.com/debauchee/barrier/
[ap]: https://azure.microsoft.com/en-us/services/devops/pipelines/
[barrier_move_azp]: https://github.com/debauchee/barrier/issues/293