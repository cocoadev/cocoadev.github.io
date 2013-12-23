---
layout: page
title: MakingFrameworksWithHeaders
---

I'm trying to make a framework so I can reuse my code. The thing, is , I have no idea how to "correctly" do it. I tried on something else before, but the resulting framework didn't have any .h files or a /sources directory, so I couldn't include my classes and use them. Are there any tutorials about frameworks hanging somewhere around the internet where I can't find them?

----

Assuming you're in Xcode: Select the target in the left pane of the project window. In the right pane, you'll see a list of files. Every header will have a popup menu next to it, set to "project". Change that to "public" for all headers that you want to become part of the framework, and off you go.

