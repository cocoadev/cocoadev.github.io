---
layout: page
title: OppositeOfWebKit
---

Are there any libraries/frameworks out their that allow you to easily generate web content.  What I want to do is use web kit to display pages inside the app and have the app write those pages.  HTML is a lot prettier than RTF which is what the alternative basically is if you want to display a lot of textual information.

As a PS, I wanted to add that I don't really want to just run a CGI or perl script from my cocoa app.  Doing so in my case would be very inconvenient, especially given the overhead of communicating the darn script... I really need something that I can call from within my cozy cocoa enviornment.  Obviously, just plugging in strings to a file isn't that bad, but it would be nice if there is already something out there.

----

You will want MiscMerge. It's great. Get it at http://www.misckit.com/ (I think). You can get it to work with RTF as well as HTML. It's a very powerful template language all written in Obj-C/Cocoa.

