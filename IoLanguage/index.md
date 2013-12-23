---
layout: page
title: IoLanguage
---

http://www.iolanguage.com/

Io is a really nifty little language, claiming inspiration from languages like Lisp, Smalltalk, Self, Lua, and I believe some others. It's a pure-object language, like Smalltalk, prototype-based like Self, has support for coroutines and futures (async messages), which are pretty neat, and has a very simple syntax.

In Io, everything is reduced to a message-send. It uses odd syntax from an ObjC coder's point of view:     receiver message(arg1, arg2) but once you're used to it, it's actually pretty nice.

RobRix and others (feel free to mention yourselves if you wish, guys) are working on a project called Europa that, among things, allows Io to use ObjC-style messages (since in Io, selectors can contain colons, it just doesn't use infix syntax like ObjC does).

It's also got really lovely support for OpenGL/GLUT, an ObjC bridge which I haven't yet used, and I think most importantly, it's fun.

Performance is apparently 'pretty good', and getting better all the time.

* IoObjcBridge

