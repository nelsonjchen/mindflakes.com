---
date: 2015-11-29T00:37:59-08:00
tags:
- meta
- blog
title: Switched to Hugo
---

*Wow*! I think it has been nearly two years since I last updated this blog.
I have decided to change the blog a bit and port it over to [hugo][] which is
a static site generator written in Go. It is *very* fast compared to [nanoc][]
which I was using. I do not know if I quite agree that this is just as
customizable as [nanoc][] but it is clearly faster.  I was not taking too much
advantage of the previous [Foundation][] based flexibility of the [nanoc][]
site and I believe it may had become a burden. Also, bitrot was beginning to
seriously set in. Luckily, it is a static site but it does make some tears shed
when you have to pin some really old gems and use a really old version of Ruby
to build and deploy the site. Unless the kernel syscalls change dramatically,
I will probably be able to compile with this version of [hugo][] indefinitely.

Anyway, the `nanoc` versions troubles me no more. I am using a [hugo][] version
of the site now. As an improvement, I have also setup automatic deployment of
my `master` branch to Google's storage and configured CloudFlare to provide TLS
and all the goodies that come with that. I guess I could also start using
something like [prose.io][] to write my posts as well. I doubt I will go that
far! But, it is very nice to know that is possible.

I am now just using a theme called [hyde-y][] which is off-the-shelf. Of
course, I did customize it a little bit. [hugo][]'s overriding system is plain
and simple to use.

* Why would anyone want summaries on the front page? Just give the complete
  content.
* The titles of the posts on the front page should have the same information.

I addressed all that, and while it was a bit of a hunt, it was fairly clear
what needed to be done.

I do not think I will go the full mile with customizing `hugo` like I did with
`nanoc`. The complexity was hurting more than it helped. I guess I have
switched over to being more minimalist since I concieved the original site.
[Foundation][] is great if you need the kitchen sink and everything but it is
really overkill. It was also another maintenance burden with its rather
relentless upgrade schedule. I am taking it slow from here on out.

[hugo]: http://hugo.spf13.com
[nanoc]: http://nanoc.ws/
[hyde-y]: https://github.com/enten/hyde-y
[prose.io]: http://prose.io
[Foundation]: http://foundation.zurb.com
