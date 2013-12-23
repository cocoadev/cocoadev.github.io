---
layout: page
title: UpdatingComplexSimulationGUI
---

So, I've been writing an application that uses a physics simulator and a lot of opengl as well as a "brain" engine -- these are all safely & separately threaded and work beautifully. However, my GUI, which is pure Cocoa ( the physics, the brain and the graphics are c++), needs to keep updated to reflect what's happening in the simulation -- since I can't really use callbacks as the environment is continually changing, I use timers to poll the simulation and keep the GUI synchronized. 

*What do you mean by �the environment is continually changing�? That there is no natural place to make the callback?*

** yes -- everything's continuous so there's no way to shoehorn an event/trigger type situation, since everything's position/velocity/tension/feedback mechanisms etc are constantly changing **

Now, my app is quasi document-centric, so I have a main window representing the simulation and various floating panels representing accessors and control mechanisms. The main window and each panel have separate timers -- where the panel timers start when the panel is shown and stopped when the panel is hidden. So, without grepping the source to be certain, I believe I have maybe 8 or 10 timers, because in some situations I have lightweight tasks updated more often and heavier tasks updated less often in the same panel.

What I'm wondering is this: is my approach bad? How lightweight is General/NSTimer? I've done a fair amount of low-level system programming in my days and I've implemented timers before, where a timer instance was pretty light, just something that told the General/RunLoop that it needed to be called every N millis or so. The timers I implemented were light enough that it made sense to make a new timer for every instance where it was needed. Is General/NSTimer any different?

Is there any compelling reason for me to condense my timers into one "master" timer which then delegates calls out to those who need it?

*That is really difficult to say... if timers does not currently make up a bottle-neck, then probably not -- but I guess that is what you don't know, and would prefer seeing the source for General/NSTimer to decide how effectively it works :)*

**I feel the same way -- though I consider looking into General/GNUStep, even though the likelyhood of shared implementation is nil **

Anyway, I have another question. Does anybody have any performance tips for General/NSTableView? When I started porting my work from Qt on linux to Cocoa, I quickly discovered that my original code, which made heavy use of dynamically updated tables and outline views was eating the performance of my app. I've had to take some interesting detours in gui design to avoid the use of tables and outline views for anything but largely static data ( as in, only changing when a document is closed or a new one is opened ). Under Qt I could have cells in a table update 20 times a second with negligible CPU hit.

*While I also find it a bit frustrating that Cocoa is generally rather slow compared to many other frameworks, you may ask yourself if you really need to update the table 20 times a second -- i.e. just skip 19 of the 20 updates.*

** yes, but when you're scrutinizing realtime feedback 1 second can be a long time. I've compromized via a decent logging system and doing, say 2 or 3 updates per second **

I'm well aware that Cocoa's MVC approach leads to good design -- and I appreciate it -- but Qt's hoarier table design was much more efficient. So, does anybody know any tricks for making General/NSTable lighter? perhaps ways to only update certain rows or columns? 

*For General/NSOutlineView there is     reloadItem:reloadChildren:, but there is in equivalent to General/NSTableView :( though I think that the class already use     isEqual: on your objects to minimize the updating, so if your data source can provide the objects without overhead (i.e. not constructing objects each time) and     isEqual: is fast (i.e. no deep comparisons of arrays or similar) then I would think that updating the text in only a single row should be relatively fast -- but I do not know this for a fact.*

** perhaps, if I could make certain that instead of returning freshly allocated objects of the same value for unchanged cells I could return the same General/NSString instance. That would require a lot of background changes, but might eke out a little performance **

*Have you actually run a profiler on this code? Everything is useless speculation until you do that. If it turns out, after running a profiler, that your code is spending a lot of time getting objects for the table, or running isEquals: on the returned objects, or things like that, then you can spend the time to make that part faster. Shark lives in /Developer/Applications/Performance Tools/CHUD/ and it is an excellent profiler. I would really recommend spending some quality time with it before trying to decide what to do next, if you haven't already done so.*

--General/ShamylZakariya

*You should btw be aware that much of the slowness of Cocoa comes from the fact that all rendering happens with the CPU in main memory and is then uploaded to the graphics card as a texture -- so the bottleneck is rarely the program logic, but instead the rendering parts, and for example by updating first and last visible row in a table view you will cause the entire table view to redraw, which is very slow, because it redraws the bounding box of all changes (this has been improved for Panther, but many classes probably still use this old system).
You can actually calculate the update speed -- if you can transfer 100 MB/s over the bus and the table view is 800x600 then 54 updates pr. second is all you can do (and you can't do much else in that second).*

----

Ultimately, I think I'll not worry too much about either thing. Performance is good so far by keeping data that needs to be updated quickly in General/NSTextField labels ( instead of tables ). And the whole General/NSTimer thing is moot since I've not seen any performance trouble -- I was just wondering if it was a General/BadThing.

--General/ShamylZakariya
