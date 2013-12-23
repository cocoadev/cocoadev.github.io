---
layout: page
title: FlushingAppEventQueue
---

First, I'd like to say off the bat that I know this is a Bad Thing. It also happens to be pretty damn close to unavoidable, so please I ask in advance to consider my situation and not lecture me on design.

The situation is this -- I've got a very, very, very complicated physical simulation & OpenGL drawing system that acts as a simulation environment for  AI test bed development. The actual "agents" are threaded, they run happily on their own and the API they use to control motors, read sensors etc is threadsafe by design; my simulator is just one backend for their IO. The host simulation program however has proven by testing not to be particularly threadsafe, even though I've attempted my best ( and I do have a fair amount of multithreaded dev experience; mostly from my BeOS days ).

As such, the host program is largely single threaded; that is to say, the OpenGL code and the simulation code run in one thread -- the primary application thread. Other matters, like Quicktime export, logging facilities, etc run in their own threads. I tried to make the physics and OpenGL run in a separate thread and had little success. Ultimately, it proved not worth the effort, particularly since I never had anything that blocked the app thread until yesterday.

So, yesterday I introduced large, high resolution terrains for my agents to explore. The terrain "engine" I wrote needs to do a fair amount of processing when it's laoded -- primarily to do things like quadtree-based similar-normal decomposition, lightmap calculations, VBO uploading and other jiggery pokery. It's not slow, per se. It takes about 5 to 10 seconds, depending on complexity and other parameters like the agressiveness of simplification. It is, however, slow enough that when you open a simulation using the new terrain engine the NSOpenPanel sheet freezes and the whole app goes unresponsive until the terrain is loaded. Of course, once it's loaded it runs just fine. It's the loading process that's biting me here.

What I'd like is two things:

First, to be able to "pump" the event queue periodically, to allow the app to close the sheet and to prevent the beachball.

Second, to be able to open a window with a progressbar and to show completion amounts as the terrain is processed. I don't like long processes without feedback.

My intial thoughts would be to have something like the hypothetical code below:

    

- (void) flushEventQueue
{
   id event;
   while( event = [theApp nextEventMatchingMask: ... )
   {
      [theApp processEvent: event];
   }
}




Any ideas? 

--ShamylZakariya

----

If it's at all possible, you should modify your code to perform processing in chunks, and call it from a timer. It sounds like this isn't possible for you, due to the nature of the library, but I thought I'd mention it anyway.

You can probably do this with the runloop, by calling     -runUntilDate: with     [NSDate date] or something. It won't remove all pending events, just one, so you should call it very often.

Otherwise you might be able to use     -sendEvent:. The docs say you shouldn't call it directly, but it just might work.

----

I don't think timers will do it, though I'm a big fan of them. I had no idea that timers would be so well implemented in Cocoa. They're really quite a viable alternative to threading, in many cases.

Regardless, I'll give a shot at     -runUntilDate:. It sounds promising, even if it only processes a single event. Fortunately, my terrain code operates as a whole boatload of discrete & fast calculations, so I ought to be able to insert calls to process events in my loops.

Since my simulator is cross-platform c++, my plan is to implement a generic c++ progress notification api, with a cocoa backend that will call      runUntilDate:  and will handle showing a window and progressbar.

Thanks for the lead,

--ShamylZakariya

----

Hey Shamyl,

You say that the open panel gets stuck when you are loading a user chosen terrain. I'm not sure if this will work, but try getting the path informationn first, have the runloop cycle once and then load your terrain.

    

- (void)getTerrainPath {
    [terrainOpenPanel beginSheet.....
    NSArray *filenames = [terrainOpenPanel filenames];
    [self performSelector:@selector(openTerrainsWithFilenames:) withObject:filenames afterDelay:0.0f];
}

- (void)openTerrainsWithFilenames:(NSArray *)filenames {
    // you get the picture    
}



--zootbobbalu

----

So, I implemented a test ( on a testbed app which also uses my terrain code ) over lunch and it has... interesting... problems. The actual mechanism itself works beautifully, a window pops up, the progressbars and whatnot work like you'd expect and app events are being flushed.

The trouble is... timer events are being flushed too. Of course, this is a classic threading style problem and I know of several ways around it. What happens is that timers registered to redraw the window are being called while the terrain is still being generated. This causes all the expected race condition issues. I gave a stab at simply setting a      done  boolean to block out the code that shouldn't execute until the loading process is complete but that causes other issues which I won't get into. I can solve this, of course, but I'm wondering if there isn't a better way...

What I'm wondering here is if there's a way to simply block the firing of all or specific timers for some period, and re-enable them when I'm done. Is there a generic way? I don't see any methods on NSRunLoop that seem encouraging, and while the CFRunLoop carbon API allows the enumeration of all installed timers, I don't know if CFTimer is toll-free-bridged to NSTimer.

Do I just have to kill the offending timers and re-create them when I'm done? I don't like the sound of that because I want to keep my code generic... and having to know which timers to kill seems like a good way to get yourself in trouble 6 months down the road. Sounds brittle, to me -- I'd rather just halt them all ( without having to destroy them ) and re-enable them all when I'm done. But what impact is that likely to have?

Any ideas?

--ShamylZakariya

*According to the TollFreeBridged page, CFTimer is bridged to NSTimer.*

----

Instead of using the plain     -runUntilDate:, use     -runMode:beforeDate: and specify a mode other than NSDefaultRunLoopMode, like NSModalPanelRunLoopMode or NSEventTrackingRunLoopMode, or make up your own. Your timer should only be scheduled in the default mode, so it will stop running. You can use this strategy to stop other things from running as well.

----

I can't use the      -runMode: beforeDate:  approach, even though that looks nice. My display timers are set to run in all modes so the animation will run while I drag sliders and do file operations. It's important that it works this way...

Anyway, I got it to work by simply calling      -setFireDate:  on all display & physics timers, by making a singleton TimerManager class with      -stopAllTimers  &      -startAllTimers  methods. It's not what I really wanted originally, but it's better than nothing at all!

If anybody's interested, my ProgressNotifier classes are pretty simple and seem to work nicely. I can put it in the SampleCode.

From C++ I can just call:

    

// mark the "sub task" of "Some Task" to 10% completion
ProgressNotifier::notify( "Some Task", "Some subtask", 0.1 );

//or
ProgressNotifier::notify( "Some Task", "Some subtask", ProgressNotifier::Indeterminate );

//and when I'm done -- this hides the window and restarts timers
ProgressNotifier::done();



And the Cocoa backend will load the nib file and take care of showing the window, pumping events, and keeping the progress bar up to date ( or set to barber-pole mode ).

The C++ class is just a wrapper to an identical ObjectiveC class. All my core code is cross-platform c++, so it's important that the progress notification system has a C++ interface.

--ShamylZakariya

