---
layout: page
title: UpdatingUIWhileBusyWithoutThreads
---

I'm playing about with openssl at the moment, and things like key generation can take a while... I've got a progress bar (in indeterminate mode, sadly!) which will happily spin in threaded mode while the key is being generated, but the rest of the UI doesn't get updated.

I know that threads is the proper way to fix this, and I'll do that next - for the sake of convenience while hacking about I was going to use this method I came across before... Unfortunately, I can't remember where I found it! If I recall correctly, it allowed updates to the UI while doing things like sitting in loops around blocking sockets and that sort of thing, and I think 'yield' may have been in the method name - I've been googling for some time and still haven't found *anything* like it, though... If I programmed in assembly language, I wouldn't get nearly as many false positives in my searches! ;) - *false positives* hmmmm is that possible? :p

Does anyone know what I'm talking about? No longer entirely convinced that I do! ;)

Thanks,

General/RufusCable

----

Well, for non-blocking I/O, usually you would use a *select* (or some non-blocking General/NSFileHandle call which does something similar) but non-blocking computation isn't the same thing.  The I/O can be done in a non-blocking way only because the thread is idle while sitting in a syscall, otherwise.  With computation, however, the only way to do it in a non-blocking way is to have the computation run in small segments.  If you are using some General/OpenSSL library routine then this will not be possible (unless the expose calls to do it in small parts).

The best way to do this would probably be to get General/ThreadWorker and use it to make the key generation call asynchronous.  You could then continue to handle UI events until you get the callback that the key is ready.  It uses threads but I don't see any way around that, nor would it be a problem in this kind of situation.

--General/JeffDisher

----

I gave up the search in the end! I've been rewriting it to use threads, roughly following the example with inter-thread communication in ABY's book. This problem is an odd one, though; although the key generation call is a blocking call, it can use a C callback function to provide details of its progress (it tells you what's happening, but not how much is left to go!). My intent was to use these callbacks to keep the UI looking busy... ;)

I'll take a look at General/ThreadWorker, but I need to do it the hard way at least once so I know I'll appreciate the problems it solves - if I haven't done so I know I'd end up using it as a magic black box. Black boxes are all very useful (isn't that one of the pleasures of Cocoa?) but I suspect a little understanding is helpful with multi-threading problems... ;) Almost finished now - I'll try v3 (well, v.03! ;) ) with General/ThreadWorker next!

Thanks!

General/RufusCable

----

Okay... the key generation stuff now runs in a separate thread and updates the UI through callbacks to the main thread. General/NSLog shows that my callbacks and updates to controls in the main window are all working - but the updates still aren't reflected on the screen until my thread completes! My 'update progress indicator' callback method is currently just adding a dot to the text in a control in the window (an General/NSTextView at the moment, but I tried an General/NSTextField too in case it made a difference! ;) ).

I assume that changes in intelligent controls such as these two automatically call     -setNeedsDisplay, but I tried adding that... No joy. Tried calling     -displayIfNeeded on the main window - nothing. Got really desperate and put     General/[[NSApp mainWindow] display] in my callback method (yuk!) - still nothing. I've experimented with moving the callback function into the main application delegate class, but that hasn't made a difference either.

It looks to me like it's being clever and not bothering with display updates while the processor's busy doing other things (though that's why I'm trying threads in the first place!)... I tried lowering the priority of the thread in case - still no joy. What am I missing here?! :-\\

Thanks,

General/RufusCable (I'll add a page detailing the solution if I ever get it working!)

----

Be careful not to update the views/controls directly from the callbacks. The callbacks are most likely within the context of your secondary thread. Cocoa is not very thread safe, you should use performSelectorOnMainThread:withObject:waitUntilDone: to update the controls from the main thread. I suspect that this is contributing to your problem of the controls not updating correctly.

----

No, General/ThreadWorker callbacks are from the thread which asked for the asynchronous operation.  That is why General/ThreadWorker is so useful.  It uses General/NSConnection objects to allow the asynchronous callback into the main thread to work as a distributed object connection.

As for background threads updating the UI:
Updating UI from non-main threads has been safe since 10.0 (there were issues during the public beta when doing background thread updates of General/OpenGLViews, at least).  The only problem is that some things behave somewhat strangely with respect to certain UI ideas.  For example, I have a program where I create a window and display it from a background thread.  It doesn't receive focus as it would if posted from the main thread (ie:  the user has to click on it, first).  I haven't ruled out mistakes in my code as the reason for this, though.

--General/JeffDisher

----
Hmm, First, considering he mentioned he was going to do it without General/ThreadWorker first; I assumed that is were he is at. Second, I was referring to the openssl key generation callbacks he describes, not those of General/ThreadWorker (I have never bothered w/General/ThreadWorker). I personally have had Cocoa UI problems while updating controls/views/windows from a secondary thread, like your statement "things behave somewhat strangely", which brings into question the thread safety. More specifically, I have seen the problem he describes, controls not updating as they should. In my specific case, I performed those calls on the main thread using the afore mentioned performSelectorOnMainThread and all was well again.

Regards

----

Well... both suggestions sounded promising, so I've looked at both! The     -performSelectorOnMainThread sounded like it would mean fewer changes to my code, so I've tried that first....no change! That was puzzling, as it the "not updating from main thread" problem sounded like it was going to be the main reason for all this, so I added a couple of before-and-after     General/NSLog("%@ %@", self, General/[NSThread currentThread]); calls and discovered I was already doing the updates from the main thread! That explains why it didn't make a difference, then, but it doesn't help with the updates problem!

I've had a look through the General/ThreadWorker source code. It's much cleaner than mine (though I understand it better today than I would have done yesterday, I think!) but I'm essentially doing the same thing. I'm already using General/NSConnection to communication between the threads, so the parent thread controls the child worker thread with start: and stop: and so on, while the callbacks made by the worker thread use rootProxy to access the callbacks in the parent thread, confirmed working by the General/NSLog calls above. I don't think using General/ThreadWorker will fix the problem here, then - I think it'll misbehave in a considerably cleaner and more elegant fashion! ;) I'll try it when I get home this evening.

I'm tempted to try adding calls in the worker thread to force it to sleep and see if the UI gets updated while the thread sleeps (only for experimental purposes - obviously my multi-threaded app would then be *slower!*). Otherwise I may resort to Plan B - your_call_is_important,please_hold.mp3 playing in the background while the key is generated... ;)

Let me know if any of you want a copy of the code (it's very short at the moment, as the problem showed up almost straight away!) to look at - not asking anyone to fix the problem, but if ever you want an example of one that *doesn't* work...... ;)

Thanks for the suggestions - I'm not giving up yet!

General/RufusCable

----

Hi Rufus,

The method you're looking for, I believe, is - General/[NSView display].  I may have completely missed the point here, though... since I can't beleive you've been searching for that one liner...

*It's possible, since every mention of     display also seems to say "You should never do this"*

----

Hi -

I've already tried     -display but that didn't work. I didn't expect it to, mostly because General/NSTextView and General/NSTextField are fully-fledged controls, and I'd expect them to be intelligent enough about marking themselves for redisplay whenever they're changed through accessor methods.

I think the reason the books say don't is that if the controls or views already work properly,     -setNeedsDisplay:YES should do the trick as the OS should then send out the appropriate     -drawRect to the views involved etc.

There's a     -displayIfNeeded which presumably avoids the redraw if nothing's done a     -setNeedsDisplay:YES. I've tried all of these and none of them worked, but if they *did* I'd still trace the calls, look for the reason they worked and remove them again - they seem like a dirty solution to me!

I haven't quite got to General/ThreadWorker yet, having been distracted by someone else's code... See General/ChickenOfTheVNC :)

General/RufusCable

I should add a note to explain the problem more concisely: I'm now updating the UI by calling update code in my main thread from a worker thread (not a General/WorkerThread yet! ;) ) and can confirm from General/NSLog and the debugger that these are being called throughout my worker thread's work. However, the OS appears to queue these updates (which are mostly     -setStringValue and     -replaceCharactersInRange:withString: calls) until the worker thread has finished before *displaying* them, despite them being called from the main thread. I think it's queueing them until the system is idle, rather than until the worker thread finishes, but it ends up *looking* like the same thing! ;)

General/RufusCable

From what I understood, i think the     -setNeedsDisplay:YES is recommanded because if several calls are made (because of several modifications), it will do the drawing only once, at the end of the current iteration of the run loop. You call     -display to force IMMEDIATE redrawing without waiting for the next iteration of the run loop. So it really looks like     -display might be what you are looking for. Are you sure you gave it a fair try? If you did, are you sure they were really called from the main thread (not sure it would matter so much, though)? Sorry I can't come up with anything new, but I wanted to point out what I thought is the difference between     -setNeedsDisplay:YES and     -display, which apparently you did not understand that way :-) --General/CharlesParnot

----

There has to be something fundamentally wrong here. I have applications that use the performSelectorOnMainThread to set text fields values and increment progress bars from threads spawned straight from General/NSThread detachNewThreadSelector:toTarget:withObject. They work flawlessly and there is no need to call display or setNeedsDisplay:. Although, I use setObjectValue not setStringValue for my General/NSTextField(s), I don't know if that is the difference, but off the top of my head, I don't think it should be.

----

Solved! Although I'd already tried     -display and     -displayIfNeeded, I was invoking them on     General/[NSApp mainWindow]... Invoking them directly on the General/NSTextView works perfectly!

Playing with the working code, I've found that using     -setNeedsDisplay:YES makes no difference, which is to be expected - the control has already flagged itself as needing redrawing the the contents changed.     -displayIfNeeded works fine, confirming the flag was set.     -display is unnecessary here, then, as the conditional one works. I guess the overall conclusion is that the display *will* automatically update during a quiet moment, but *does* need forcing if you want it to happen *now* while a thread's churning away.

Now all I have to do is remove a rash of     General/NSLog and     performSelectorOnMainThread: calls - the latter was always unnecessary as I'm using General/NSConnection to perform selectors on the main thread already... :)

Thanks all!

General/RufusCable

----
What is the     General/NSConnection buying you that     performSelectorOnMainThread does for you anyway? The point is, by setting the value on the main thread (the General/GUIs thread) the inherent update will be registered on it's (the main threads) run loop and updated on the next run. Again, ANY forceful update should be unnecessary. I also prefer this approach in the face of multiple threads, so the user will see all updates by any of the threads, not just the end result of all of them on the next update of the GUI.

----

The worker thread has to communicate with the main thread for the updates described, which of course could easily be done with     performSelectorOnMainThread, but the main thread also needs to communicate with the worker (start/stop/pause, and more to be added later). I don't know if there's an easier way than General/NSConnection, but this seems reasonably future-proof. The original example came from ABY's Cocoa Programming book, chapter 24, and was the first time I'd needed two-way communication between threads.

If there's a cleaner or easier way, I'll certainly investigate! I still intend to give General/ThreadWorker a try :)

General/RufusCable
----
I see, I was under the impression that the thread communication was trivial (produce the key and update some GUI items).

----
What I said about the difference between     -display and     -setNeedsDisplay:YES does not mean that you have to use     -display, as the main thread is not stuck so its run loop should take care of display updating, as needed, once you set the flags (as explained above). I have in some very specific occasions that the     -display might make things look better in having speedier dispay update (but can slow the working threads... perceived speed vs real speed!). This is really something that you will have to judge when the whole thing is running.     -display is evil in most cases, but is still there to use.... --General/CharlesParnot
