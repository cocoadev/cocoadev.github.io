---
layout: page
title: BindingsAndQuartzComposer
---



I currently have a Quartz Composition as my main view and I have a input "string".  I have that input bound to a NSTextField and when I update the textfield and hit enter it updates the value on the Quartz Composition however if I use the following in my code:

[textField setStringValue:@"100"];

The NSTextField displays the value 100 but it does not update my Quartz Composition with that value.  If I am to erase "100" and type it in myself the display will then update and display.

I have viewed http://developer.apple.com/technotes/tn2005/tn2146.html#TNTAG4 as an attempt to bind the composition directly to a variable but that tutorial is for UI elements.  Would anyone have an example on binding a quartz composition input to a variable directly (NSString) or to a NSTextField that can be modified in code and just not on the NIB and still have it update?
----

*This is normal; updating fields programmatically does not affect bound values. Why are you doing that? If you need to set some value in your composition to "100" then set that value directly rather than setting the text field. The bindings then should work properly to make sure that the text field reflects the value it is bound to.*
----
I would however I'm not too sure how, I'm very new to Cocoa (started this application on Thursday afternoon).  Just a little personal application but I would like to learn as I go along and this was one thing I couldn't find a tutorial on or documented very well.

Do you have an example of how I could bind the input on the composition directly to the value?
----

    [yourCompositionController setValue:@"100" forKeyPath:@"theBoundKeyHere"]

