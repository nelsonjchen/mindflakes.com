---
title: "And suddenly, nearly a decade later, a blog appears! Again."
created_at_time: 22:05:53-07:00
---

I'm ashamed to say I've neglected this domain and site for 9 years. In that
time I've gone through high school and university in that time. In each period,
I've attempted [putting up blogs, forums, another blog, and other similar
nonsense](http://web.archive.org/web/*/http://mindflakes.com). I did not follow
through with whatever resolutions I may have made to keep the site maintained
or write down the latest results from my last screwing around with my projects.
Basically, for the past 9 years, I don't really have much to show for it other
than being able to rattle off some helpful anecdote from experiences I have
never written down here and there.

> Remember kids, the only difference between screwing around and Science is
> writing it down.
>
> -- Adam Savage, [Video](http://www.youtube.com/watch?v=BSUMBBFjxrY)

I thought I was conducting Science for the past few years; in reality, I've
only been really screwing around.

Most of my experiments in the past few years have also ended in spectacular
failures. I never wrote about my failures even during the times I had a blog
available. This is where my fails turned into an epic fail. I recently watched
a [TED talk](http://www.ted.com/talks/ben_goldacre_battling_bad_science.html)
on this and learned that this phenomenon is called research bias.  I should not
have withheld what I had learned from failing. My memory of my failures only
fade away until someone mentioned it and I rattle off some anecdote. This
obviously won't scale and does not do much to help me demonstrate any skills
when I can't even get my foot in the door.

I'm going to change that with the launch of my new site. I'll be honest here,
this is very much a career move. However, it is also a personal one for reasons
I've outlined above. I can't let those many hours of screwing around go to
waste!  Prospective employers and/or clients love to hire people who do
Science, not screwing around. And I think this site will fix that.

My new site is designed is designed so much as to be a portfolio and as a blog.
My portfolio is small as I have little to show and my blog has a meager amount
of posts. As a foundation though, the site is solid and is totally ready to
showcase more of my skills.

### Because of Hope

I started the project to fix up my personal site in September 2012.

At that time I had heard about this static site generator called `nanoc`. I had
taken down previous versions of this site since I could not be bothered to
maintain the PHP beast that is called Wordpress. Also, there was little hope of
me hacking on Wordpress to change its default style to something that better
demonstrates my skills. Stock Wordpress themes just do not exhibit confidence
in web development. Also, learning PHP is just something I'me not too
interested in doing. The horror stories of PHP may be FUD, but I've experienced
and witnessed them myself in a past lifetime. `nanoc` answers both of these
problems. I can hack Ruby and static HTML pages on a server do not have moving
parts.  `nanoc` is written in Ruby and generates static HTML pages for
uploading onto a server. Compared to a beast like Wordpress, it is also very
transparent. The nanoc guys can better explain their kind of product (static
site generators) [here](http://nanoc.ws/about/#why-static). I did have
a problem in that I had absolutely no experience with `nanoc`.

In September 2012, I answered a request for help from an organization
named Because of Hope at the beginning of my last quarter at UCSB. Because of
Hope is an organization that helps children and women in Uganda through
sustainable methods. Here's the email that was sent to the UCSB Computer
Science jobs list:

> My name is Natalie Lemonnier and I graduated from UCSB in 2011. Since
> graduating, two of my friends and I have started a nonprofit here in
> Goleta called Because of Hope (BOH) that is working to empower women in
> Uganda.  I am emailing you because our nonprofit is getting ready to
> launch a new Student Sponsorship campaign, which will require making
> some changes to our website, and I was wondering if you knew of anyone
> (student, faculty or otherwise) who might be interested in helping us
> make these changes.  I am not sure if websites are your area of focus,
> but I just wanted to contact you and see if you could point us in the
> direction of someone who might be able to help.
>
> Originally, our friend built our site from scratch.  However, he is no
> long working with us and none of us have any web background to make the
> necessary changes.  I believe that some of the fine-tuning will be
> relatively easy, while other issues may be more time intensive.
>
> However, because we don't have web background, we are not really sure
> of the exact time needed to complete the desired changes.  If you or
> anyone else you know would be interested in volunteering to help us, or
> would like to learn more about our situation, we would greatly
> appreciate it.
>
> Because we have only been operational for a year, no one in our
> organization currently gets paid, so unfortunately it's not in the
> budget to pay someone to help us with our website.  But we are hoping to
> find someone who would be understanding of our situation and wiling to
> help us further our mission!  I know UCSB is getting ready to start up,
> so please feel free to pass on this information to students you think
> would be willing and able to get involved.  Please let us know if anyone
> comes to mind! Thank you for your time, and please feel free to contact
> me via phone if that would be at all more convenient for you! (818)
> 620-8847. Happy Fall Quarter :)
>
> (If you would like to learn more about our organization or view our
> site, please feel free at http://www.becauseofhope.org.)
>
>
> Best of Wishes,
>
> Natalie Lemonnier

So, I needed some experience doing a `nanoc` site,  something nice to put on my
résumé, and something to satisfy and fulfill the "help poor children in Uganda"
request in the `Vim` text editor. Because of Hope needed someone to maintain
their site.  With my résumé as sparse as it is, I called up Natalie and offered
to help.

I met up with them and took a look at the setup. They had a $20/month VPS to
serve one HTML file. The rest of the "pages" were actually linked tumblr posts.
I told them upfront I wanted to test out some tools on their problem before
I ran with it on my own personal site. In the meantime, I would maintain their
current handcrafted site and check out any technical details such as the domain
name, Google Apps, or the VPS itself.

I had `nanoc` chosen out, but what about the rest? I checked out the
then-current website's code and it had some custom 980px grid system.
I couldn't be bothered to try and understand it. I did try to understand it
enough to add a donations page that they immediately needed for their upcoming
fundraiser.

On the bright side, all the mockups that Nikki Day of BOH did were in
Photoshop. They were far more ambitious than what the current site's current
implementation. The custom 980px grid system was not up the the task. In
retrospect, it would have been thrown out anyway. I shudder at what beast
I would have had to slay if the original plans had gone through.

I chose to use [Zurb Foundation](http://foundation.zurb.com/). Zurb Foundation
is a front-end framework that is far more flexible than many other frameworks
out there. This was the the kind of Framework that could handle Nikki's
designs. I would also get free functionality such as drop-down menus, pretty
buttons, photo carousel, and first-class support for SCSS, an extension of CSS.
Zurb Foundation's use of SCSS was very instrumental in adapting the design of
Zurb Foundation for Nikki's designs. Compared to its competitor Twitter
Bootstrap, there is far more control over elements on the page, especially the
buttons.  Also, I got a mobile site for free! BOH didn't even ask for it but it
came with it!

Ever since from that time, I've been working on BOH's `nanoc` site. I learned
how to better compose layouts, the Rules DSL of nanoc, and the whole `nanoc`
ecosystem. I was also working with it to integrate Zurb Foundation into the
project.

Remember that $20/month VPS? It has since been replaced with Amazon S3. I also
tossed in Cloudflare in front. BOH can take on a DDOS attack of the most epic
proportions. Why would anyone attack a non-profit is beyond me but the overkill
just tickles my funny bone. And the cost is free to near pennies! This is
something only a static site can do. As a vote of confidence in S3, [NPR used
S3 for transferring data in its election
app](http://blog.apps.npr.org/2013/02/14/app-template-redux.html). I'm not
saying that BOH will become as big as the presedential election, but it can
definitely handle that much traffic if need be and certianly more than
a $20/month VPS. Honestly, I'm just glad I don't have to manage a Linux server
to achieve this goal. It's really upload and forget.

Also, in doing `nanoc`, I now realize how much of a pain in the ass it is to
update the original site. There was so much copy-paste! How do people do all
these busy work?

I learned a lot from doing the BOH site and I am continuing to work on it in my
spare time. It's source code is versioned in a Git repository so I can
experiment on it all I want. It's also open source in a [GitHub
repository](https://github.com/becauseofhope/because-of-hope). If anybody wants
to peruse it and reuse parts of it, they are more than welcome to do so. I plan
to keep it in sync with anything that is a `nanoc` best-practice and update it
with new pages.

### Mindflakes

Okay, so I've helped a non-profit get their site stuff in shape and beyond.

What about my personal site?

Ever since I've started the BOH site project, I've probably made 3 false starts
in creating mindflakes.com. I wager I wasn't confident enough in my `nanoc`
knowledge in the previous starts. I had given up or I did not have enough time
to really sit down and really start work on my site. Also, I was working part
time at a local company in the area at this time. I came back home so exhausted
from looking through piles of Ruby and Python that I really did not want to put
my finger to the keyboard.

Maybe the fourth time was the charm? I didn't start the mindflakes
project this time. What I did instead was start an [example/bootstrap
project](https://github.com/crazysim/nanoc-foundation-blog) for a blog that
used Zurb Foundation and `nanoc`. I put some of the best-practices I knew into
there and learned some more best practices from various sources around the web.
I'm especially proud that the project is completely tied to Zurb Foundation as its
Javascript is sourced from its gem. There were plenty of Bootstrap examples out
there but I believe that Zurb Foundation better fits with the `nanoc`
philosophy in letting power come out in the right places when needed.

By distilling the framework from the content of my site, I was able to work
away from any pressure in having a nice site to present. Just having a good
framework to build upon is much more gratifying.

Not only that, I now have a free reusable framework that I may be able to reuse
for client projects that don't involve my site. I don't have to extract magic
or best practice out of my site. I can just rebase anything off of the
framework with the way I like it. I plan to contribute anything I think is
relevant while building my own site back to the framework.

This is my first long blog post since ever. I wrote this to test out this blog.
As of this post, Mindflakes.com is still very much out-of-the-box even though
I just built the box. I will customize it and make it look pretty enough to
really showcase my work over the next few sessions. I'm going to have to decide
what to do about images and what to do with the flexibility that Zurb
Foundation offers me.

As for what to write, I think I have that settled.
