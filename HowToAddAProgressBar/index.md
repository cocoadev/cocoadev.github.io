---
layout: page
title: HowToAddAProgressBar
---

To add a progress bar you need to first go into Interface Builder.  Grab a progress bar from the "Cocoa-Other" palette and put it on your window.

Now, to actually have the progress bar do something, you need to associate it with code.  Make the progress bar an outlet for the class in which you will use it (if you do not know how to create an outlet see HowToUseOutlets).  Once you create the outlet, go into Project Builder.  The following text should appear in between the braces of your class implementation file
    
IBOutlet NSProgressIndicator *myProgress;

Here "myProgress" is the name of the outlet (you can choose your own as you please).

Now, usually the progress bar is meant to give the user information as to how much time is left in an activity.  This is often a repetative activity, such as you find in a loop.  In Interface Builder's info palette, you can set the range of the values in the progress bar.  I like to set it to read values from 0 to 100, so that the values I send the bars can represent the percent of work completed.

In Project Builder, you can set the value of the progress bar at a particular time using the following method
    
[myProgress setDoubleValue:myVariable];

So, if myVariable was 20, the progress bar would fill up to the level of 20 (or one fifth of the way).  If myVariable is in a loop, you can have it change so that it starts at 0 when the activity has just begun, and reaches 100 when the activity is done.

To make sure the progress bar redraws itself each time you change the value, you can call the following method...
    
[myProgress displayIfNeeded];


To reset the value of the progress bar back to zero, you can type...
    
[myProgress setDoubleValue:0.0];


If you would like for the progress bar to disappear when the value is zero, you can type...
    
[myProgress setDisplayedWhenStopped:NO];


----
One thing to remember is that this code only works with determinate progress bars.  You use determinate bars when you know how long a process will take, such as downloading a file that you know the size of.  Indeterminate bars just have the spinning stripes and show that something is happening, but you don't know how long it will take.  Before you use the above code, make sure you type...
    
[myProgress setIndeterminate:NO];


----
For an indeterminate progress indicator, invoke startAnimation: to start the animation (the spinning of the barber pole) and stopAnimation: when the task is complete. By default, the delay between animation steps is one twelfth of a second (5/60). You can change the animation delay by invoking setAnimationDelay:. Setting the delay to a double value larger than the default value will slow the animation, while setting the delay to a smaller value will speed it up.

Instead of invoking startAnimation: and stopAnimation:, you can control an indeterminate progress indicator directly by sending the animate: message. Each time you invoke animate:, the animation advances by one step. You can speed up or slow down the animation by varying how often you invoke animate:. Like other views, a progress indicator redisplays itself on each pass through the event loop, if needed. To ensure immediate redrawing, however, you can invoke the displayIfNeeded method (inherited from NSView) each time you invoke animate:.

If your application is threaded, you may want the progress indicator?s animation to happen in a separate thread by calling setUsesThreadedAnimation:. If your application doesn?t already use threads, creating a thread just for the progress indicator can actually slow down your application.

For an example of  this see ProgressIndicatorInToolbar

By default, a progress indicator is indeterminate.
----

One thing to consider about startAnimation: and stopAnimation: It isn't reflective of real-world progress. So, if your app's thread hangs in the middle of its loop, the progress indicator will happily keep on spinning, thus telling the user it's fine while actually it isn't. Best practice in such cases is to call animate: instead. This method is what's called by the timer each time it fires and advances the animation by one frame. So, by calling this periodically from your loop, the user will have feedback that is much more true to what is actually happening. -- UliKusterer

----

* This is from http://developer.apple.com/techpubs/macosx/Cocoa/TasksAndConcepts/ProgrammingTopics/ProgIndic/index.html *

*Edited by DerekCramer*

Back to HowToProgramInOSX

----

It's possible, depending on what you're doing, that as you increment the value of your progress bar nothing will happen -- nothing.  You don't need to spawn an extra thread or anything for the progress bar; that's usually a bad idea;  instead, if you're running the two stanard threads (the UI, which is spawned automatically by the Cocoa runtime system) and your main thread (which is also spawned automatically at application startup), then you probably want to say [myProgressBar display]; after each update to its value.

----
(This is an auto-response)
To put a progress bar in a NSPanel, make the panel appear when the process begins, and then disappear when the process is finished use:

    
myProgress window] center];
[[myProgress window] makeKeyAndOrderFront:nil]; // nspanel was originally hidden in Interface Builder
[[myProgress window] display];
for(i=0;i<max;i++)
{
	[myProgress setDoubleValue:100*i/(double)max]; // supposing that the max value for the [[NSProgressIndicator was set to 100 in Interface Builder
	[myProgress displayIfNeeded];

	/* do my stuff */
}
myProgress window] orderOut:nil];


How can I do to have a stop button breaking the loop? Thanks!
--[[RoTo

----

This is not the correct way to display a progress bar for a long-running task. You should either make the panel modal or make it a sheet. Then do your stuff in an NSTimer. To cancel, have a button in the panel/sheet that simply ends the modal/sheet session, and stops the timer. You should be doing this even if you don't want a cancel button, as using a loop will stop your app from processing events, displaying the spinning pizza of death and generally causing bad things.

