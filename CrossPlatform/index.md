---
layout: page
title: CrossPlatform
---



CrossPlatform code is code that compiles and runs on more than one platform, the more, the merrier. It's hard to do well because of endianess of the processor, the availability of libraries and frameworks, et cetera. C technically *should* be CrossPlatform, but in reality, you tend to have to code to more than the ANSI headers.

POSIX is a nice attempt at making C portable to a reasonably large set of platforms.

Java is CrossPlatform, and ScriptingLanguages can be because they tend to be very high-level. To my mind, it would seem that most high-level languages, including ones like Haskell and ML, should be reasonably portable.

---

Linux Journal has an article on Byte and Bit order [http://www.linuxjournal.com/article.php?sid=6788].  This is/can be an importan issue when trying to write cross platform programs.

