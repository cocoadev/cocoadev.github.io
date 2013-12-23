---
layout: page
title: AutoWindowResizing
---

I'm sorry if this is an easy question, but how do you make a window resize in a smooth fashion with new contents? If you don't know what I'm talking about, iTunes, Safari, and Acquisition all do it (check the pref. pane)... They all use a NSToolBar I believe. I can set up a toolbar easily, but the whole smoothieness is just out of my league. This is a really cool feature and I want it.  ; D

-- JohnDevor

----

Check out NSWindow's -setFrame:display:animate: method.  -- Bo

Or just check out the bottom of the page NSWindow. The code is sitting right there. -- MatPeterson

----

Alright, the code works great, but I've got another related question. How do I use NSViews change the content of the preference panel I am trying to set up? Simply, my question is how do I insert a view (such as myView) into the method -setFrame:display:animate: ? I don't think I can just say: 
    [window setFrame:myView display:YES animate:YES]
Can a view and all of its contents act as a frame?

-- JohnDevor

----

what about this ?

    [window setFrame:[myView frame] display:YES animate:YES]

-- PtxMac

----

Take a look at the setContentView: method of NSWindow. Basically, what you need to do is create different "standalone" views in IB (along with a blank view) and hook them up to your controller. Make sure you retain all these views in your code before you start swapping them. When different toolbar buttons are clicked, you can set the content view of the window to the blank view, animate the window based on the frame of new view (use NSWindows's frameRectForContentRect:styleMask: method), and then set the content view to the new view. That should do it. (I haven't tried this, but I'm pretty sure it would work.)

Hope that helps,

-- JaredWhite

----

see also UFISpringyPanel and WhereIsCoolSystemPrefsWindowEffect

