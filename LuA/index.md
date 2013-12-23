---
layout: page
title: LuA
---



LuA (Spelled "Lua", the Portuguese word for "moon", from Latin "luna") is a cool scripting language built to run within existing applications. It's main purpose is to contain preferences, but it can do so much more.
The main website that hosts Lua version 5 is http://www.lua.org, and they also have a wiki.

Lua was also one of the main inspirations for IoLanguage; if you like Lua, you might like IoLanguage as well, and on OS X, IoLanguage has a nice ObjC bridge. It's nice seeing well-designed languages like Lua and Io interoperate with Cocoa, isn't it?
----
If someone created a Lua-ObjC bridge, I'd be in heaven. All I want is a few functions to change between NSDictionaries and all the basic data-types (NSString, NSNumber) and their Lua counterparts... Lua is a wonderful language. I think everyone should give it a shot. --RossLeonardy
----
Ask and ye shall receive: http://luaforge.net/projects/luaobjc/
----
Wow. That's better code then I could ever write *sniff*... it's so beautiful!  Mind if I change it a bit? I think that perhaps instead of returning an int for a Boolean type (I'm only looking at the last function right now)  you (and others that use this code) might want to change it to a BOOL. But maybe the increase isn't worth the extra "if" statements. Also, um... I think you forgot to include Foundation.h. Meh, it's easier to be a critic then to be someone who actually does something about it.
----
Please feel free to make whatever changes you see fit, and to use it in any way you like. I've released it as public domain. --ToM
----
Let me get this straight -- this ObjectiveC bridge must be compiled into the LuA implementation directly, right? It isn't an Addon for ... <ahem> ... existing LuA implementations?

Also, 

*Meh, it's easier to be a critic then to be someone who actually does something about it.*

I thought the opening statement said it all: * Wow. That's better code then I could ever write *sniff*... it's so beautiful!  Mind if I change it a bit?* ROTFL!

-- MikeTrent
----
The bridge can be be incorporated into Objective-C projects (e.g. as a plugin), or it can be compiled as a loadable library for existing LuA implementations. -- ToM.


----
You might want to give Wax a shot, it's a Lua ObjC bridge. It's designed primarily for the iOS devices, but can easily work with any Apple Device. http://github.com/probablycorey/wax
-- Probably Corey

