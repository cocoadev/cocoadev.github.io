---
layout: page
title: CoreAnimationInGames
---



What do you guys think?
Will Core Animation be of any use in gameprogramming?
Will it be fast enough?

----
To be of any use in game programming, it would need a collision detection system.  I don't know if it has.

----
To get an answer to that before Leopard is released you will probably need to get an ADC membership to allow you to download, because I don't think, many of us are willing to risk getting the boot for talking about a prerelease product when under NDA.

----
Yep, don't worry... that only costs 500 bucks, your first-born child, and your eternal soul...

----

And the owners of Cocoadev would get in trouble too. Fun for all.

----
Sincere question: why would you use such a non-portable library to implement games? Has the mac market grown that much, or are you just doing this as a hobby?

----
Well, I was just curious if it was even doable... Could be fun to try :)

----
Why use such a non-portable library to implement *anything*? Are games somehow more port-worthy than other software?

----
Just a question, he gave the best answer I can think of; fun. And yes, games ARE more port worthy (to that OTHER user friendly *cough* OS), at least in business and user community terms.

----
Why are games more port-worthy, is it just because people's expectations are lowered as far as user interface and taking advantage of the platform when it comes to games?

I remember a lot of very old Mac games had nice Mac-like interfaces with windows and menu bars and scroll bars and the whole works. You'd have a strategy game that put its map in a normal Mac window, with normal Mac scroll bars to look around. Now games insist on using their own junky GUI widgets and taking over the entire screen. I'm sure this makes them easier to port if you use cross-platform graphics libraries like OpenGL.

However, considering how many PC games use DirectX and then get ported to the Mac, this can't be a huge concern.

----

Oh yeah, I remember those types of games. The MacVenture series was a great favorite when I was kid:

http://en.wikipedia.org/wiki/Uninvited_%28game%29

http://en.wikipedia.org/wiki/MacVenture

----
I guess it could be great for 2D-scrolling jump and run games like super mario or supertux, for that matter. But what do I know? --MatthiasGansrigler

----
Having played a fair amount with Core Animation, I believe it would be very difficult to implement a lot of the things necessary for even a simple game. The purpose and beauty of Core Animation is that it provides implicit (or simple to configure explicit) animations for user interfaces, where it was previously very difficult to do such a thing without using some other package such as OpenGL, or trying to do it yourself.

I can imagine that it might be possible for someone with a lot of brains and copious free time to encapsulate Core Animation and extend it with the features you need for games, but it would lose a lot of its simplicity, which is really tuned for user interfaces and not intended for games at all.

