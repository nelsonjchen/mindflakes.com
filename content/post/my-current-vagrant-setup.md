---
title: "My current vagrant setup"
date: 2013-06-22 20:09:42-07:00
---

I firmly believe that [Vagrant][1] is the quickest way from nothing to a running and preconfigured development
environment on any machine and especially Macs. For me, the ['works on my machine' problem][2] is the biggest reason I
run Vagrant. Day-to-day though, Vagrant is probably the easiest to use UI for [Virtualbox][3]. If my work actually
had money to give me for the [VMWare plugin][4], I believe it would be a better UI for VMWare Fusion as well.

### The Basics

This is enough to get started with Vagrant and to reap the rewards.

1. [Virtualbox][3]
2. [Vagrant][4]

That's great and all, but these are the basics. At the very least, you'll be able to bring up some boxes that don't
require special plugins up.

### Frills

You don't need these but I do! I usually build my [Vagrant][4] boxes with [Opscode Chef][6],
a configuration management system. For reference, a Chef cookbook is a series of statements about how a machine
should be setup.

Most of these frills are plugins. To build the boxes I make, you'll usually have to install or use these.

1. [Berkshelf][5] is a dependency resolution manager for Chef. I use Berkshelf as a gem along with the corresponding
[vagrant plugin][7]. With this, when I run `berks cookbook <name>`, I can  make a Virtual Machine that can be created
 and destroyed quickly from scratch for whatever purpose. I could do `vagrant init` but `berks cookbook` has it beat
 by creating a directory structure that's pretty much a Chef cookbook. Even if I don't intend to redistribute said cookbook,
the VM made is perfectly fine for tryout purposes.
2. I like using the [Opscode Bento][8] boxes. They are minimal and they have already been uploaded to S3 on Opscode's
 dime. In a `Vagrantfile`, you can set the box URL for a Vagrant basebox to be downloaded. These boxes are great.
3. You can't use the Opscode Bento boxes without the [Vagrant Omnibus plugin][9]. Those boxes *do not include Chef*
so you must install Chef at runtime.
4. Just so you don't get warnings about the Virtualbox additions being out of date,
there's a [Vagrant plugin to automatically update the guest additions if needed][10]. This one is really *optional*
and it's use just surpresses that warning you get if you bring up a vagrant box with old guest additions.


### The Future

In the future, I'll like to be able to test my boxes to make sure they stay working as the things they pull from the
internet change. For this, there's [Test Kitchen][11].

Unfortunately, it's still really cutting edge. However, there are [guard plugins][12] and this [Youtube Video and
blog post][13]. That video is very much a must see for anybody who appreciates TDD.

Full integration testing on your own laptop is very attractive to me. I keep mine plugged in and I find it disturbing
 that the rest of the cores on this MacBook Pro just lie cool.

And also, maybe if I get some cash, I might drop some money on the VMWare plugin and VMWare Fusion. If I want to
simulate multiple servers at a time and heavier loads, it would make that much faster.

[1]: http://vagrantup.com
[2]: http://docs.vagrantup.com/v2/why-vagrant/
[3]: https://www.virtualbox.org/‎
[4]: http://www.vagrantup.com/vmware
[5]: http://berkshelf.com/
[6]: http://www.opscode.com/chef/
[7]: https://github.com/riotgames/vagrant-berkshelf
[8]: https://github.com/opscode/bento
[9]: https://github.com/schisamo/vagrant-omnibus
[10]: https://github.com/opscode/bento
[11]: https://github.com/opscode/test-kitchen
[12]: https://github.com/opscode/guard-kitchen
[13]: http://starkandwayne.com/articles/2013/05/07/tdd-your-devops-with-test-kitchen/
