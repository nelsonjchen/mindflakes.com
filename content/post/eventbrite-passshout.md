---
date: 2019-03-15T00:00:00-08:00
tags:
- kotlin
- android
- eventbrite
title: "An Android Accessibility Service for Eventbrite Organizers: PassShout"
---

The Scene:

* It's Saturday. And it's like so many people have free time on that date.
* You're manning a convention center ticket check-in booth.
* Your eyesight just isn't the best. After looking at hundreds, if not
  thousands of them, it can be a blur.
* You're using Android.
* There's a long line and people have _different_ pass or ticket types,
  sometimes even if they are in the same group.
* You're processing thousands of people.
* You might not even have a table.
* You're using Eventbrite Organizer.

I have a tool for you! It's called [PassShout][passshout].

In addition to a chime, it'll read out loud the pass/ticket type.

This greatly reduces operational mistakes. PassShout is part of an
implementation of a method in occupational safety called
 [Pointing and Calling][pac]. [PAC][pac] has been
 [adopted widely thoughout Asia and the NYC Metro][nyc-asia-adopt-pac] to
 reduce incidents on trains systems. By calling out the type, things move
 faster and mistakes are reduced.

## Implementation

This is the first Android app I've written in Kotlin. It's amazing what the
Android Accessibility API offers for third party programs to make things
more accessible. Unfortunately, there does not seem to be an equivalent
for iOS.

Kotlin is really easy to read and write. I can see how it's not Scala and 
is closer to a usable modern Java. It's basically the Swift of Android. 


[pac]: https://en.wikipedia.org/wiki/Pointing_and_calling
[passshout]: https://github.com/nelsonjchen/PassShout/
[nyc-asia-adopt-pac]: https://www.atlasobscura.com/articles/pointing-and-calling-japan-trains
