---
title: "GitHub Wiki SEE: Search Engine Enablement"
date: 2021-07-27T00:00:00Z
tags:
  - github
  - wiki
  - seo
  - rust
---

I built this in March, but I figure I'll write about this project better late
than never. I supercharged it in June out of curiosity to see how it would
perform. It's just a ramble. I am not a good writer but I just wanted to write
stuff down.

TLDR: I mirrored all of GitHub's wiki with my own service to get the content on
Google and other search engines.

If you've used a search engine to search up something that could appear on
GitHub, you are using my service.

https://github-wiki-see.page/

Did you know that GitHub wikis aren't search engine optimized? In fact, GitHub
excluded their own wiki pages from the search results with their
[robots.txt][ghbrobots].
["robots.txt" is the mechanism that sites can use to indicate to search engines whether or not to index certain sections of the site][robots_info].
If you search on Google or Bing, you won't find any results unless your search
query terms are directly inside the URL.

The situation with search engines is currently this. For example, if you wanted
to search for information relating to Nissan Leaf support of the openpilot
self-driving car project in their wiki, you could search for "nissan leaf
openpilot wiki". Unfortunately, the search results would be empty and would
contain no results. If you searched for "nissan openpilot wiki",
"https://github.com/commaai/openpilot/wiki/Nissan" would show up correctly
because it has all the terms in it. The content of the GitHub wiki page is not
used for returning good results.

It has been like this for at least 9 years.
[GitHub responded that they have declined to remove it from `robots.txt` as they believe it can be used for SEO abuse][gh_issue].
I find this quite unbelievable.
[The mechanism to prevent this is to utilize `rel="nofollow"` on all user generated `<a>` links in the content and with newer guidance suggesting `rel="nofollow ugc"`][nofollow_guide].
By removing the content from the index, they have blinded a large portion of the
web's users to the content in GitHub wiki pages.

Until I made a pull request to
[add a notice to their documentation that wikis are not search engine optimized][official_github_doc],
there was absolutely no official documentation or notice about this decision. I
still believe it is not enough as nothing in the main GitHub interface notes
this limitation.

I believe there are many dedicated users and communities that are not aware of
this issue who are diligently contributing to GitHub wikis. This really
frustrated me. I was one of them. Many others and I contributed a lot to the
[comma.ai openpilot wiki][op_wiki] and it was maddening that their work and my
work were not indexed.

I noticed that a lot of technical questions had Stack Overflow mirrors rank
highly and yet they weren't Stack Overflow.

So what I did was this: I made a proxy site without a `robots.txt` that would
mirror GitHub wiki content to a site that did not have a restrictive robots.txt.
I put it up at https://github-wiki-see.rs. I called it GitHub Wiki SEE for
GitHub Wiki Search Engine Enablement. It's kind of a twisted wording of SEO as I
see it as enabling and not even "optimizing".

## What's the technology behind it?

The current iteration is a [Rust Actix Web][actix_web] application. It parses
the URL and makes requests out to GitHub to get the content. It then scrapes the
content for relevant HTML and reformats it to be more easily crawl-able. It then
serves the content to the search engine and users who come in from a search
engine with a link to the project page and reasoning at the bottom as a static
pinned element. Users aren't meant to read that page and are only meant to
browse to the original GitHub page.

With these choices, I could meet these requirements:

* Hosted it on Google Cloud Run just in case it doesn't become popular.
* Made it easy to deploy.
* Made it cheap.
* Made it small to run on Cloud Run.
* Made it low maintenance.

Another considering was that it needed to not get rate limited by GitHub. To do
this, I made the server return an error and quit itself on Cloud Run if it
detected a rate limiting response to cycle to a new server with a new client IP
address. Unfortunately, this limited the number of requests each server could
handle at a time to a minimum of one.

## Supercharging it

I originally built the project in March. I monitored the
[Google Search Console][gsc] like a hawk to see how the openpilot stuff I had
contributed to was responding to the proxy setup. Admittedly, it got a few users
to click through here and there and I was pretty happy. For a month or two, I
was pretty satisfied. However, I kept on thinking about the
[rest of the users who lamented this situation][gh_issue]. While some did take
my offer to post a link somewhere on their blog, README, or tweet, the adoption
wasn't as much as I had hoped.

I had learned about [GH Archive][gh_archive] which is a project that archived
every event of GitHub since about 2012. I also learned that the project also
mirrors its archive on [BigQuery][bigquery]. BigQuery allows you to search large
datasets with SQL. Using BigQuery, I was able to determine that about 2,500
GitHub Wiki pages get edited every day. In a week, about 17,000. A month? 66,000
pages. My furor at the blocking of indexing increased.

I decided to throw $45 at the issue and run a big mega-query and queried all of
GH Archive. Over the lifetime of GH Archive, 4 million pages were edited. I
dumped this into a queue and processed it with Google Cloud Functions to run the
`HEAD` query on all the pages in the list and re-exported it to BigQuery. 2
million pages were still there.

You can find the results of this in these public tables on [BigQuery][bigquery]:

* `github-wiki-see:show.checked_touched_wiki_pages_upto_202106`
  * Checked with `HEAD`
* `github-wiki-see:show.touched_wiki_pages_upto_202106`

With this `checked_touched_wiki_pages_upto_202106` table, I generated 50MB of
compressed XML sitemaps for the GitHub Wiki SEE pages. I called them seed
sitemaps. The source for this can be found
[in the sitemap generator repo][sitemap_generator].

BigQuery is expensive, so I made another sitemap series for continuous updates.
An earlier version used BigQuery, but the new version runs a daily GitHub
Actions job that downloads the last week of events from GH Archive, processes
them for unique Wiki URLs and exports them to a sitemap. This handles any
ongoing GitHub Wiki changes. The source for this can also be found
[in the sitemap generator repo][sitemap_generator].

## The results

I am getting thousands of clicks and millions of impressions every day. GitHub
Wiki SEE results are never at the top and average a position of about 29 or the
third page. The mirrored may not rank high and I don't care as the original
content was never going to rank high anyway so the content appearing at all in
the search results of thousands of users even if it was sort of deep satisfies
me.

*All in all, a pretty fun project. I am really happy with the results.*

* My contributions to the OpenPilot wiki are now indexed.
* Everyone else's contributions to their wikis are now indexed as well.
* I learned a few things or two about writing a Rust web app.
* I learned a lot about SEO
* I feel I've helped a lot of people who need to find a diamond in the rough and
  the diamond isn't in the URL.
* I feel a little warm that I have a high traffic site.

I have no intention of putting ads on the site. It costs me about $25 a month to
run but for the high traffic it gets, the peace of mind I've acquired, and the
good feelings of helping everybody searching Google, it is worth it.

I did add decommissioning steps. If it comes to it, I'll make the site just
directly redirect but after GitHub takes Wikis off their `robots.txt`.

## Things I Learned

This is a grab-bag of stuff I learned along the way.

* Sitemaps are a great way to get search engine traffic to your site.
* Large, multi-million page sitemaps can take many months to process. Consider
  yourself lucky if it parses 50,000 URLs a day.
  * The Google Search Console saying "failed to retrieve" is a lie. It means to
    say "processing" but this is apparently a long standing stupid bug.
* GoogleBot is much more aggressive than Bing.
  * Google's Index of my proxy site is probably a lot larger than Bing's by a
    magnitude or two. For every 30, 40, or even 100 GoogleBot requests, I get
    one Bing request.
  * This is important for serving DuckDuckGo users as it is their index.
* The Rust Tokio situation is a bit of a pain as I am stuck on a version of
  Tokio that is needed for stable Actix-Web. As a result, I cannot use modern
  Tokio stuff that modern Rust crates or libraries use. I may port it to Rocket
  or something in the future.
* A lot of people search for certification answers and test answers on GitHub
  Wikis.
* GitHub Wikis and GitHub are no exception to Rule 34.
* Cloud Run is great for getting many IP addresses to use and to swap out if any
  die. The outgoing IP address is extremely ephemeral and changing it is a
  simple matter of simply exiting if a rate limit is detected.
  * This might not always work. Just exit again if so.
* GCP's UI is nicer for manual use compared to AWS.
* The built-in logging capabilities and sink of GCP products are also really
  nice. I can easily re-export and/or stream logs to BigQuery or to a Pub Sub
  topic for later analysis. I can also just use the viewer on the UI to see what
  is going on.
* I get fairly free monitoring and graphing from the GCP ecosystem. The default
  graphs are very useful.
* Rust web apps are really expensive to compile on Google Cloud Build. I had to
  use 32-core machines to get it to compile in underneath the default timeout of
  10 minutes. I eventually sped it up and made it cheaper to build by building
  on a pre-built image along with a smaller instance but it is still quite
  expensive.
* The traffic from the aggressive GoogleBot is impressively large. Thankfully, I
  do not have to pay the bandwidth costs.
* My sloppy use of the HTML Parser may be causing errors but RAM is fairly
  generous. It's Rust too so I don't really have to worry that much about
  garbage collection or anything similar.
* Google actually follows links it sees in the pages. When I started, it did
  not. Maybe it does it only on more popular pages.
* The Rust code is really sloppy, but it still works.


[sitemap_generator]: https://github.com/nelsonjchen/github-wiki-see-rs-sitemaps
[bigquery]: https://cloud.google.com/bigquery/
[gh_archive]: https://www.gharchive.org/
[actix_web]: https://github.com/actix/actix-web
[nofollow_guide]: https://developers.google.com/search/docs/advanced/guidelines/qualify-outbound-links
[gh_issue]: https://github.com/isaacs/github/issues/1683
[robots_info]: https://en.wikipedia.org/wiki/Robots_exclusion_standard
[ghbrobots]: https://github.com/robots.txt
[official_github_doc]: https://docs.github.com/en/communities/documenting-your-project-with-wikis/about-wikis
[gsc]: https://search.google.com/search-console/about
