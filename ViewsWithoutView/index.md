---
layout: page
title: ViewsWithoutView
---

I am designing a layout-y application with two intended interfaces.  The first creates the layouts on your own screen like any other application, the second is a CGI (web forms) program.  That's the concept anyways, but the piece I am having trouble on is having a non-graphical application generate the view and serialize to pdf to send in response to the form.  (I don't want windows popping up on the server all the time...)  Is there a nice way to tell a view to print itself to a file without the view being visible or contained in a window or anything...
