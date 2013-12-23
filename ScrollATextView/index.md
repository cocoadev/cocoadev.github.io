---
layout: page
title: ScrollATextView
---

JediKnil here. I know the general code used to automatically scroll a text view to the bottom:
    
[textView scrollRangeToVisible:NSMakeRange(textView textStorage] length], 0)];

But in my case (a rather strange one) I need some other solution. What I have is [[ObjC controller code that uses Java to interact with a JavaCC parser. To print error messages, I passed in the NSTextStorage for my text view, got an NSMutableStringReference (the Java equivalent of NSMutableString), and used appendString(). This sends off notifications to the text storage...and to the layout manager, as I found out. Unfortunately, placing the code in a delegate method for either one doesn't quite work right. If I put it in textStorageWillProcessEditing: or textStorageDidProcessEditing:, the text view has not yet received the changes, and so the range (the length of the string) is out of bounds. If I use layoutManager:didCompleteLayoutForTextContainer:atEnd:, it works, but it doesn't scroll all the way to the bottom, as if the text storage was updated *after* the layout manager was informed (clearly impossible). Where should I put the call to scrollRangeToVisible: so that it works properly?

I know that the easiest solution is to just make all changes through NSTextView, but I'd really prefer not to, as that's inefficient, looks worse, and would require me to go back and change everything.

----

Have you tried doing it from a delayed perform (delay of 0) to see if that helps?

----

No...I found a different solution. I didn't want to use delays because most of the time they start up another thread (at least the way I understand it), and I'd have to hardcode another value (delay time) that might not work on a slower computer. However, I have a working solution...if unwieldy and kludgy. Set a flag when textStorageDidProcessEditing: is called, then scroll when layoutManager:didCompleteLayoutForTextContainer:atEnd: is called (this makes sure a seperate call to the layout manager won't cause a scroll). Put it a bit of code at the end of my Java call (back in ObjC) that adds an empty string to NSTextView, using replaceCharactersInRange:withString:. My next problem was stopping the autoscroll if the user was manually scrolling, but I couldn't solve that either...So I just called setHasVerticalScroller: on the scroll view to turn off the scroll bar while text was appended, and back on at the end, so the user can go over the output. It actually works pretty well...but I'd still like to see other people's ideas.

--JediKnil

----

Delayed performs most certainly do *not* start another thread. They basically just wrap a timer, which is handled by the runloop in the main thread. There's also no risk of hardcoding an arbitrary value. Use a value of 0 like I said, and the delayed perform will execute as soon as you get back to the runloop. Since there's no multithreading going on, it's entirely predictable when it will fire.

----

Oops, sorry. There *is* multithreading: my Java code is launched in a new thread to allow the user to change other stuff in the meantime. I don't know how this will affect the workings of a timer in the runloop...and I don't know what thread the delegate's in, though I suspect it is the main one. I have little experience with delayed performs, so I'm sorry for my misunderstanding. --JediKnil

