---
layout: page
title: SimulateTyping
---

I thought I'd seen another page which addressed this issue, but I couldn't find it through extensive searches, so here goes: I'm looking for a solution which types text into another program, or inserts some text into the program's FirstResponder all at once.

My application pops up when a user hits a hotkey, then it hijacks keyboard input and processes it. When it's done processing the input, I'd like it to drop some new text into whatever window was open beforehand. This is possible with AppleScript and the relatively new UI Scripting stuff, but I'd rather avoid this.

-- RyanGovostes

----

This is pretty much what input servers are for. Search it at ADC.

----

It sounds almost like you should be making a Service.

To answer the original question, although I'm not sure if it's the best way to do what you want to do, CGRemoteOperation.h has functions for simulating user input.

