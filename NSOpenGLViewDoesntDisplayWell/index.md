---
layout: page
title: NSOpenGLViewDoesntDisplayWell
---

Well, I created a program which uses a subclass of NSOpenGLView to show graphics and lets the user move and rotate the objects using the mouse. It works perfectly. The problem appears when I want to include it in a bigger project. This project is document based. Each document window has a NSTabView and one of its NSTabViewItems contains my subclass of NSOpenGLView. Now the view doesn't display well and only redisplays its contents when the window is moved out of the screen and placed inside again.

Does anybody know why could this be happenning?

Thanks in advance. Angel David Puerto.

----

I have used a custom NSOpenGLView in an NSTabViewItem successfully, and cannot remember the problem you're describing ever appearing. The only time I've had problems like you're describing is when I was calling OpenGL initialization functions at the wrong time. Have you tried to simplify the document window until the problem disappears? You will really have to narrow things down and/or show us code if you want to find the problem

� BrentGulanowski

