---
layout: page
title: WildWindows
---



For them as don't follow the cocoa-dev/gl lists closely, you may find this interesting.

http://www.atzenbeck.de/research/wildWindows/

----

Please be very careful if you use this.  Check out the last couple of messages referenced in the mailing list link.  This code uses undocumented Apple General/APIs that could change / break in the future, and if too many people end up using these undocumented General/APIs, Apple could be painted into a corner, being unable to improve some part of Quartz because too many apps are using undocumented features.  But yeah, it's a cool demo.

----
I doubt many people would/could use something like this as its usefulness is not that great. Also, I doubt Apple would be worried about breaking API's, they have done it before in 10.0, 10.1, 10.2, 10.3, etc. 

----
Yeah, I think that last bit about undue pressure on Apple is not worth worrying about.  It's very clear that Apple will change unpublished General/APIs if they feel like it.  If some person evaluates and decides they want to use it anyway, that's their business.  

Besides, putting pressure on Apple to include similar features in *some* published API is perfectly reasonable, and that's the only kind of pressure this is likely to exert.

(Oh, and yeah, this is pretty useless. :-) )

----

You never know where something like this could end up, but at the beginning
it always looks a little like the guy (gal?) who discovers a new body part and starts
shouting *OOOOOOH! Look what I can do with THIS!!*

----

But think of the possibilities, your app detects a known pirated serial number, and so all the windows start rotating  around. :-)

----

Think of the possibilities if you could actually access the third column of the General/CGAffineTransform matrix that is set for a window. 
If you could, you would be able to twist the window (projective transform). This would be amazing, since you could have a Window Manager/Expose extension that allowed you to rotate a window to the side of the screen, allowing you to keep configuration windows tilted (still in view) in such a way that you would have more room for the main window you are working with. 

As a concrete example, take iterface builder, and make it so that the paletes and info windows can be tilted allowing you to design a large window, tilting in and out the palettes as you need them.

----
Didn't Single Window Mode from the OSX Developer Previews do something like this?

----
Hmm, yeah.  We could have a spiffed up version of windowshade where windows lie back (instead of completely collapsing).  Then you could tell the difference easier between different collapsed windows.

----
I'm curious as to how that was done - I imagine the effect of a window rotating backwards could be simulated through shearing and scaling, which should be possible with the first two rows of the affine matrix... but a proper projection transform using Quartz seems impossible, since affine matrices don't store the 3rd row/column (depending on your viewpoint).

----
Notice, also, that when you have a window rotating, and you go into Expose, the window is miniaturized, but it's still rotating. Kinda cool :)

----

I'm the author of the "Rotated Windows" demo app, so I feel I should quell a few impossible expectations.  Sorry. :)

First, the General/CFAffineTransform struct only has 6 fields, for the first two columns of the general matrix.  Whether or not the window server includes the third column - which presumably it must, to perform the maths properly - there is no apparent way to do so.  I'm still investigating all the undocumented General/CoreGraphics stuff, but I've seen no evidence that the third column can be manipulated.

Also, as many of you will have noticed, things like minimising and using Expose reset the window to it's normal state at the start and the conclusion of animation, which is a bit of a shame.  There is no workaround for this short of hacking into the Dock to replace the relevant functionality with fixed versions.  Apple most likely chose to work in this non-perfect fashion for simplicity and possibly performance; determining the Expose layout for arbitrarily scaled and rotated windows would require a notable amount of extra CPU time, and more importantly to do properly (rather than a simple min-max box method) would be algorithmically difficult.

A last note - the General/WildWindows web page is a little out of date; incoming events are in fact transformed properly, so the window is almost entirely usable as normal.  There are a few issues - pop-up menus and the like (i.e. any further windows) appear in their "normal" location.  Drawers also appear in their normal location, although I do have some unreleased code which resolves that issue somewhat.  Note also that the window server does something tricky for window dragging, which means you can only drag by the top N pixels of the rotated or scaled window (where N is the height of a normal title bar), and it must also intersect the actual drawn title bar... meaning if the window ends up upside down, you can't move it at all.  Also, in the case of metal or textured windows, there's some weird stuff going on which means you can only drag from some points in the window, not others.

The dragging issue can probably be resolved simply enough, but the others I've mentioned not so.

----

When I first saw this, I thought "This could be useful for app notifications" -- General/JoeRanieri

----
Want to explain that?

----
Sure :). I was thinking of an IM client that when a new message came, would scale the window in and out. But that might be too intrusive... -- General/JoeRanieri

----
Someone has implemented this idea as an Adium plugin: http://forums.cocoaforge.com/viewtopic.php?t=605

----

General/DesktopManager (on General/SourceForge) patches the Dock at runtime, so it should be possible to fix the dock to have it check the tranformation matrix. The Genie minimizing effect the dock uses most likely would need to access the third column of the matrix in order to stretch the window in the way it does since the effect does not keep the original window as a parallelogram (the only possibility without the thrid column). Additionally, it would be nice to be able to apply the projective transformations to bezier paths in views and what not as well. I really don't see why this functionality (projective transform matricies) is not offered. I'm continuing to investigate the problem. Unfortunetly, I don't know where the General/CoreGraphics functions are defined (which object/shared library). I'm mostly interested in writing a new window manager (and would like to do it using GL instead of Quartz), and I've played around with your rotating windows code a little bit, and I will try to provide some code back when time allows.

----
General/DesktopManager patches the dock to prevent it re-instating it's superuser privileges with the window user - trying to change the docks behaviour in any significant manner is a lot to ask.  It might be possible to just no-op whatever code resets the transform, but...  I've stooped as low as going through the decompiled assembly for the General/CoreGraphics framework; doing the same for the Dock **and** writing new assembly to do whatever is well beyond my present interests.  If anyone else wants to do so, please do by all means. :)

Beware, though - I've seen evidence that the genie effect is implemented in the window server itself (ick; very bad design), so it might be a matter of patching the window server, not the dock... and I find the window server's already unstable enough without poking it brutally in that fashion.

Note also that the genie effect is done by applying a vector deformation mesh to the window, not by manipulating it's transform matrix.  I haven't yet had time to figure out how to apply these meshes, but the functionality is there and available to anyone.

----
I've never seen any API that allows live vector deformations to be applied to windows. Anybody?

----
It's there in General/CoreGraphics.  As I've stated previously in various places (hopefully here too :) ), I'm pretty sure the Dock uses special built-in functions for genie (and similar) effects, but nonetheless there does appear to be an appropriate family of functions for applying mesh transforms directly.  I'm still yet to explore them, however.

----
Finally, Quartz uses General/OpenGL where hardware accelerated to perform it's drawing.  It only falls back to software where hardware acceleration is not available, in which case it's original optimised drawing routines are faster than General/OpenGL's software renderer.  It'd be interesting, actually, to test the window deformation on a non-accelerated system... from memory the genie effect doesn't work on such systems, so it may be that the transform matrix is similarly ignored.  Anyone with a suitable system want to test it?

----
Lots of people have suitable systems; I'm on a General/TiBook 400, which doesn't use Quartz Extreme, and I still get genies and everything, they're just really, really slow. Given that the Genie effect has been part of OS X since at least the Public Beta, long before Quartz Extreme, so that's kind of an odd statement.

----
I'm on an iMac 400 with a Rage 128 Pro card. Its General/OpenGL support is pokey at best -- my father's old computer with only a software renderer is just a bit worse. Genie works, though, if a bit stuttery.

----

General/CoreGraphics *is* Quartz, so I'm not sure how you'd go about using General/CoreGraphics to circumvent Quartz. But anyway, the General/CoreGraphics library is located at /System/Library/Frameworks/General/ApplicationServices.framework/Frameworks/General/CoreGraphics.framework/Versions/Current/General/CoreGraphics.

----
Please notice GL (as in General/OpenGL) instead of Quartz, so not circumventing Quartz. I was only complaining that Quartz doesn't allow for projective transforms

----

I actually have a use for this and furthermore it will add value to my program and wont be annoying or intrusive either.  If anyone is interested in what I'm going to do I'll be happy to explain after I add it to my program. ;)

----

I dont understand how you found the function to apply an General/AffineTransform to a window in the first place. How do you find these functions? -- General/SamSomething

----
Probably a General/ClassDump on the General/CoreGraphics framework. On second thoughts it seems all the headers are there, so reading the headers of General/CoreGraphics. -- General/MatPeterson

----
It's not a General/ClassDump � General/CoreGraphics doesn't contain any classes. The appropriate file is also not in the public General/CoreGraphics headers. However, programs like nm and otool can be used to find undocumented functions like this.

OK, I have figured out how to get funciton names from nm. Now how would I know what the arguments to a function are? -- General/SamSomething

----
I used otool -tv to decompile General/CoreGraphics.  Function arguments start from r3 (f1 for floating point arguments; the relative order amongst integer and fp arguments is largely irrelevant), so you can work out the number of arguments by seeing how many registers in sequence are used.  You can usually determine their generic type by seeing how they're used - it's easy to pick up which one's are pointers by looking for NULL comparisons or store's indexed by them, and you can also guess a lot of the parameters by simply following the conventions set by the public side of the API.  Also, therer's a few generic functions used internally to convert connection & window General/IDs into the relevant structures, which if present indicate the type of parameter precisely.  In the end, a bit of trial and error in General/XCode usually polishes and confirms your groundwork.  Also, some stepping through with gdb in existing programs - e.g. the Dock, the window server itself, etc - can reveal key secrets when other methods fail. - Wade Tregaskis

----

Does anybody know (or is willing to make a guess) as to whether the iChat "pop" window (the one you get when someone starts an IM session w/ you) uses "General/WildWindows".  I always liked that effect, and kinda wanted to reproduce it.

----
This effect is most likely done in the same manner as described.  While I've been poring over the General/InstantMessage framework for a while, I haven't looked a lot at the UI side to see if there's any hints regarding this.

----

I've never watached the iChat 'pop' closely enough to say - but it certainly seems like it - like the "pulse window mode of the Rotated Windows application.  Is there some way to intercept all General/ObjC calls coming from/to a particular application/class/object?  So that I could figure out what method is being called (this would also be nice for finding the Dock's genie effect)  Also, it might be useful if a tutorial of nm (and otool) were put up here.  I've used otool before, but not nm, although I don't imagine it's any more complicated than otool.

----
I'd love such a tool too.  I generally find Sampler is sufficient, if you set the interval to 1 ms.  It of course doesn't show up a lot of the tiny functions, but it gives you a good idea of the main code path.

----

When I tried to build the sample project, it didn't build, and gave me an error: "Dependency Analysis: target *targetname* has no product type; can't build it". I assumed that somethind was funky with the project file, and didn't bother to try and fix it, so I closed the project. After that I decided to return to coding on my current app, but when I tried to build it, I got the same error, with my own app. I had not edited any code between trying to build the General/WildWindows app and building my project. I quit and re-launched Xcode. No luck. Even logging out, trying to build it as a different user, restarting the computer, creating a new target, a new custom executable, and reinstalling Xcode (1.2) did not solve this problem. I tried an old project of mine, and it builds fine. What is the deal? Did the General/WildWindows project "break" my project? Do I have any options aside from rebuilding the project file from scratch?

Thanks for any suggestions.

[EDIT: I since have (frustratingly) rebuilt the project file and it works fine now. However, anyone who can explain this bizarre behavior, please do!]

--General/RobinHP

----

I know this works on 10.3 and I've been told it still works on 10.4, but what about 10.2 and 10.1.  Has anyone tried it?

----

There's a very simple reason that perspective transforms aren't available: you can affine-transform a bezi�r path by transforming its control points, but to perspective-transform it you would need to apply the transformation to each interpolated font. Oh, I remember seeing a WWDC demo movie showing scalable UI support in Tiger; it'll be interesting to see how general transforms you can do to views without bitmap scaling artefacts. :-) -- General/JensAyton

----

Except that no bezi�r paths are involved in window transformations. The window server is entirely pixel-based. ("Display PDF" is a myth, but you probably know that already.)

----

Just an update from the author of Rotated Windows, I've managed to get some extra functionality out of the window server, in the form of the genie effect (sheets soon to follow).  I've also figured out the parameters to the General/CGSSetWindowWarp function, which lets you apply an arbitrary mesh deformation to a window, but I'm not sure what format the mesh has yet - there's too much floating point computation for me to bother disassembling, so I'm hoping someone with more experience with General/OpenGL or similar techs can see it more easily.  Each "cell" (window-server's terminology, not mine) in the mesh contains 8 float's, which seems to be 4 pairs of {x,y} co-ords.  There's also a function to create a mesh from a vertex array (probably more General/OpenGL related; not my area of expertise), where the vertex array is taken as having 8*N floats as well, and these are mapped into the mesh cells with some order shuffling.  I presume then that these points define either (a) two lines, one representing the original line, the other it's deformed position, or (b) a square of some sort in deformed position, with the original position implicit from the cell's position in the mesh (i.e. assumes a regular grid mesh).  Anyway, there's plenty more info available if anyone's interested in helping out.  Contact me on wadetregaskis, at mac, dot com if you have any more info.

----

The grid format is a two-dimensional array of control points -- each one is {windowX, windowY, screenX, screenY}.   -alf

----

I'm allergic to warnings, so I took a quick pass at fixing the 5 warnings you get on Tiger.

You need "#import <unistd.h>" before calling usleep().

You need to change a few defs. Looks like lines 54, 55, and 101 of windowListController.m should all be Boolean.
