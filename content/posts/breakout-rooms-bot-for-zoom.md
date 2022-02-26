---
title: "The short lived life of Breakout Bots for Zoom Meetings"
date: 2020-09-12T00:00:00Z
tags:
  - zoom
  - sherlock
---

With the pandemic happening, it's been tough for many organizations to adapt. We're all supposed to be together! One way organizations have been staying together is by using Zoom Meetings.

At work, we've been using a knock-off reseller version called RingCentral Meetings. From looking at their competitors, Zoom has pulled out all the stops to make their meeting experience the most efficient, resiliant, rather cheapest, and reliable experience.

One of these features is "Breakout Rooms". With breakout rooms, users can subdivide their meetings to make mini-meetings from a bigger meeting. For most of the year, there has been an odd restriction on Breakout Meetings though.

*You cannot go to another breakout room as an attendee without the host reassigning you unless you are a Host or a Co-Host.*

Obviously, this can result in a lot of load upon the poor user who is desginated the host. Even the Co-Hosts can't assign users to another breakout room.

So I made a bot:

https://github.com/nelsonjchen/BreakoutRoomsBotForZoomMeetings

![the bot rename in action](https://user-images.githubusercontent.com/5363/92406673-79d2e080-f0ed-11ea-9953-5b7704811d1c.gif)

It's quite a hack, but it basically controls a web client that is a Host and assigns users based on chat commands and on attendees renaming themselves.

Last night, I just finished finally optimizing the bot. It should be able to handle hundreds of users and piles of chat commands.

This morning I learned that Zoom will add native support for switching:

https://www.reddit.com/r/Zoom/comments/irkm82/selfselect_breakout_room/

It was fun, but as brief as the bot existed, the main purposes are numbered. Maybe someone can reuse the code to make other Zoom Meeting add-on/bots?

## Technical and Learnings

* I knew about this issue for many months but I never made a bot because there wasn't an API. I waited until I was very disatisified with the situation and then made the bot. I should have just done it, without the API.
* Thankfully, by doing this quickly, I only really spent like a week of time on this total. With some of the tooling I used, I didn't have to spend too long debugging the issues.
* The original implementation of this tool was a copy and paste into Chrome's Console. This is a much worse experience for users than a Chrome extension. [Kyle Husmann](https://github.com/khusmann) contributed a fork that extensionized the bot and it is totally something I'll be stealing for future hacks like this.
* Zoom got to where they are by doing things their competitors did not and rather quickly. This feature/feedback response is great and is why they've been crushing. I feel this is a feature that would have taken their competitors possibly a year to implement. At this time, Microsoft Teams is also looking to implement Breakout Rooms.
* Zoom's web client uses React+Redux. They also use the Redux-Thunk middleware. They do stick some side-effects in places where things shouldn't but whatever, it works. It did make integration very hard if not impossible at this layer for my bot. However, subscribing to state changes was and is very reliable.
* [RxJS] is a nice implementation of the Reactive Pattern with Observables and stuff like with RX.NET. Without this, I could not fit the bot in so few lines of code with the reliability and usability that it currently has.
* With [RxJS] and Redux's store, I was able to subscribe to internal Redux state changes and transform them into streams of events. For example, the bot transform the streams of renames and chat message requests into a common structure that it then merges to be processed by later operators. Lots of code is shared and I'm able to rearrange and transform at will.
* The local override functionality is great if you want to inject Redux Web Tools into an existing minified application. If you beautify the code, you can inject dev tools too. Here's the line of code I replaced to inject Redux Dev Tools into Zoom's web client.

  Previous:

  ```javascript
  , s = Object(r.compose)(Object(r.applyMiddleware)(i.a));
  ```

  After:

  ```javascript
  , s = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__(Object(r.applyMiddleware)(i.a));
  ```

  This was super helpful in seeing how the Redux state changes when performing actions in a nice GUI.

* [fuzzysort] is great! I'm quite surprised at how unsurprising the results can be when looking up rooms by a partial or cut up name.
* The underlying websocket connection is available globally as `commandSocket`. If you observe the commands, they are just JSON and you can inject your own commands to control the meeting programatically.
* Since Breakout Room status is sent back via WebSocket, the UI will automatically update. I did not have to go through the Redux store to update the Breakout Room UI.
* UI Automation of Breakout Room UI: ~100ms vs webSocket command: 2ms. Wow!
* Chat messages are encrypted and decrypted client-side before going out on the Websocket interface. This probably doesn't stop Zoom from reading the chat messages since they are the ones who gave you the keys. I could have reimplemented the encryption in the bot but after running some replay attacks, the UI did not update. Without the UI updating, I didn't feel it was safe for the host to not know what the bot sent out on their behalf.
* Even if I had implemented the chat message encryption, the existing UI automation of chat messages hovered around 30ms. The profiler also showed that the overhead of automation wasn't that much and much of the chat message encryption contributed to the 30ms. It wouldn't have been much of a performance win if I implemented it myself.
* **Possible Security-ish Issue** - Since it isn't an acceptable bounty issue, and hosts can simply disable chat, I will disclose is possible to DOS the native client by simply spamming chat. If there is too much text, the native clients will lock up and require a restart. Native clients don't drop old chat logs. Web clients don't have this issue as they will drop old chat automatically. Last but not least, it is possible for the Host to disable chat in many ways to alleviate such an attack. I wasn't sure if to let my load_test bot out there or not because of this but after reflection, I think it's OK.
* [RxJS] is MVP. Or rather the Reactive stuff is MVP. If you can get it into a stream, it's super powerful. Best yet, you can reuse your experience from other ecosystems. I was previously using Rx.NET for some stuff at work. I'll be definitely looking at using the Python version of this in the future.
  * Though it did seem the differing Reactive stuff have different operators. The baselines are still great though and you can always port operators from one to another implementation.
* Rx is really helped if you can use TypeScript. Unfortuntately, I never got to this stage.


That said, this was fun! Now onward to the next thing.

[RxJS]: https://rxjs-dev.firebaseapp.com/
[fuzzysort]: https://github.com/farzher/fuzzysort