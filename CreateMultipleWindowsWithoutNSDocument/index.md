---
layout: page
title: CreateMultipleWindowsWithoutNSDocument
---

I am looking to have a multiwindow application without the need for NSDocuments.  I just don't need the Save/Open, etc. functionality that it offers.

Here is what I would like to do.  Create a master Controller that would create instances of NSWindowControllers in a nib, and display the windows.  Of course this would happen dynamically as the user chooses 'New' from the Menu.  So, when a user chooses New, a new NSWindowController derivative would be created (via the master controller), which would in turn display a new window being controlled by that controller.

All of the examples that I have seen, show multiwindow apps to be based on NSDocument, and anything not based on that is a single window app with one nib and one controller.

Thanks.

----
Is there some sort of question or information you're seeking here, or are you just planning on writing a log of your development?

You seem to imply that you'd like an example. TextEdit is a very good example of a multiwindow Cocoa app written without NSDocument.

----

First, thanks for being so rude.  It was not a log of my development.  I was just trying to be clear as to what I was looking for.  You answered it in your own nasty manner.  Thanks.

----
To be fair, you never really did explain what you were looking for.  But you're right, the preceding poster was a bit abrasive.  What's odd is that your post explains in sufficient detail exactly what's necessary to implement what you're looking for.  "Master Controller" in this case would make most sense as the application delegate, which would in turn spawn model controllers (depending on how your data works, you might not even need one; TextEdit uses NSTextStorage).

Now, are you looking for multiple windows per document?  Or just a multi-document app not based on NSDocument?  The two are fundamentally different, but it's easy to confuse them.

----

Thanks.  You are right.  That part is vague.  I am looking for multi-document app not based on NSDocument.  They are not true documents in the conventional sense, so I was looking to not use NSDocument, but there might be more than one of those windows available.  Your answer lets me know I was on the right track though. 

----
Jesus, I wasn't being nasty, I was simply being straightforward. Your original post *had no question*. Maybe you *were* just writing a log of your development. I couldn't tell! It seemed like you wanted help but there was simply *no indication of what help you wanted*. Why must everybody assume the worst? If you can't handle a straightforward response which may call into question some shortcomings of your original post, then get bent.

As far as the "nasty" response, what's so nasty about suggesting TextEdit?

----

How about the first sentence?  While not directly a question, I think it sums up what I was asking for.  I figured by trying to explain how I WOULD TRY to tackle the problem, I could get some advice by some experienced Cocoa developers, which I am not.  It was not a log of my development, since it has not started yet.  TextEdit is a reasonable example, and I got substantive help from someone else who is not a prick.  Thanks again.

----
So being unable to perfectly comprehend a vaguely worded request makes me a prick? If so, call me a prick!

----

The other guy had no problem asking for clarification without being a prick.  Congrats, you got the job as prick.

----

Maybe I'm just socially deficient here, but can you elaborate what part is being a prick? All I did was ask for clarification and then give a hint for what seemed like the most likely request. Which part makes it rude?

----
Prick and prick!  what is prick!

