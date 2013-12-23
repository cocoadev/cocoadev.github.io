---
layout: page
title: XcodeCrossDevelopment
---



If I want my app to run on Jaguar, as well as Tiger, but use specific technologies only for Tiger users, should I build using the Tiger SDK or the Jaguar SDK? Basically, what's the best way to build an app that will run equally well on Jaguar, Panther, and Tiger?

----

If you don't need General/UniversalBinary compatibility build against the 10.3.9 SDK and use General/NSClassFromString for any Tiger-only classes. I suppose you could do the same thing for Jaguar, but it's been awhile since I thought about it. See General/UniversalBinariesAndJaguar

(bump)

*This is *too vague* and there is no easy answer. This depends entirely on which 'specific technologies' you want to use. Some things can be easily done by just *not using those technologies* if a gestalt check says the version is too low. By not using them, I mean don't call code, don't include libraries, etc. Start with 'gestalt' - that's how you can easily get the OS version number. If you have a specific question pertaining to your specific design, post that instead. From there, we can create a page for how to make that specific technology 'version-safe' and use this page as a sign-post since - again - there is no easy answer (that covers even a majority of situations).*

[Topic]
