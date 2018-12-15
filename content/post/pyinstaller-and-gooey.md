---
date: 2018-12-15T00:00:00-08:00 
tags:
- python
- pyinstaller
- gooey 
title: Gooey and PyInstaller for easy to write, distribute, and use
  scripts!
---

What is:

* Easy to write
* Easy to distribute and portable
* Super Powerful
* Cross Platform
* Puts computer novices and professionals on the same level?

I think it's a combination of [Gooey][gooey] and [PyInstaller][pyinstaller]. If
you put those two together, you get all those above. 

* It's Python, so it's easy to write. There are so many guides online and a wide
  assortment of libraries. Most importantly, you get libraries that have a
  strong eye towards ergonomics. Python is a wonderful beginner's language that
  some people even call an executable psuedolanguage.
* PyInstaller bundles applications into a single folder or exe so it's easy to
  distribute.
* You get the full force of Python's language and its ecosystem behind you. 
* Python is cross platform, but Gooey's GUI is also as well. Don't be expecting
  to make "Destiny"-style GUIs with it. But do be expecting a surprising amount
  of native-ness.
* While PyInstaller does package the application up into a portable Python
  script, the existing command line invokation is still left for professionals.
  Simply add `--ignore-gooey` to the list of arguments. Now it's just a plain
  old script. 

The [Gooey PyInstaller Demo][demo] was presented at a DesertPy meetup in August
of 2018. It's a project showing the development of a simple argument parsing
script all the way up to a portable GUI. Future improvements to the demo include
some CI scripts and configurations.  

Demo Project Link: https://github.com/nelsonjchen/gooey-pyinstaller-demo

[gooey]: https://github.com/chriskiehl/Gooey
[pyinstaller]:https://github.com/pyinstaller/pyinstaller 
[demo]: https://github.com/nelsonjchen/gooey-pyinstaller-demo