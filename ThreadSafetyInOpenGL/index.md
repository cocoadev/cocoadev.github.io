---
layout: page
title: ThreadSafetyInOpenGL
---




Would -lockFocus and -unlockFocus perhaps be well suited to using General/OpenGL from more than one thread? Could you use it to make sure that you aren't calling General/OpenGL code from more than one thread at a time?

Also, this stuff would probably apply even if you were using a non-view-attached (that is, fullscreen or offscreen or non-rendering) General/NSOpenGLContext instead of a view. You'd just need to make sure you had a pointer to your context object that you could use instead of [glView openGLContext] as the receiver. http://goo.gl/General/OeSCu

-- General/RobRix

----

A question regarding the statement that no opengl may be called before the drawRect: method...If i need to load textures for use on my objects, what do I do? Should I have a BOOL instance variable indicating wether I did initialization, and do it the first time drawRect: is called? Or is it ok to at the awakeFromNib stage load the textures, _after_ setting the General/OpenGL context?

-- General/DavidRemahl, aka. Chmod007

----

Rob asks if we can use -lockFocus/-unlockFocus to guarantee General/OpenGL thread-safety. No. Two separate threads can lock the same view at the same time. This means two separate threads can send GL operations to the same context at the same time, which is the fastest way I know of to panic your machine.

I have prepared a source example that demonstrates lockFocus' behavior (minus General/OpenGL code so you won't panic): General/CanTwoThreadsLockFocusOnTheSameView.

This "only one thread doing GL at a time" restriction is imposed by General/OpenGL itself -- not General/NSView. It doesn't matter if you are using General/NSOpenGLView or if you have a fullscreen context. General/OpenGL is not threadsafe.

David asks if he must squeeze all of his General/OpenGL calls into his drawRect: method. No. Just make sure you set the General/OpenGL context yourself, and that only one thread is doing General/OpenGL calls at that time. My General/FadeFromBlack examples load a simple texture in -initWithFrame:, not in -drawRect. If your object is coming out of a nib you could also (probably) do this in -awakeFromNib.

-- General/MikeTrent

----

Okay. Trying to remember what I once knew about General/NSLock...

I'm wondering if it would be possible to avoid multithreaded GL crashes in your code by obtaining a lock on a singleton (or otherwise "global") object prior to calling any General/OpenGl code.

Anyhow, thanks again, Mike. I'm in the preliminary stages of writing an General/OpenGl game, and I'm learning as much as I can about it. For now, it's in an General/NSOpenGLView subclass, but not for long, I suppose (fullscreen!).

-- General/RobRix

----

Well ... 

The Assimilator screen saver module (Dozing Cat Software, find it on General/VersionTracker) needs to know when General/OpenGL modules are drawing so it can copy data out of their GL buffer and recomposite it on the sides of a cube. Of course, some popular General/OpenGL modules (ahem...) draw on a second thread and load in textures on the main thread. Without any knowledge of how these modules control access to the General/OpenGL context Assimilator will panic while monkeying around in the GL context on the main thread when the second thread is trying to draw. 

So Assimilator makes some interesting assumptions about General/OpenGL screen savers: 


* General/OpenGL screen savers consist of a General/ScreenSaverView with a single subview.
* the subview is an General/NSOpenGLView or General/NSOpenGLView subclass.
* the subview may define (if it's a subclass) a -lock and -unlock method, which is not related to -lockFocus or -unlockFocus.
* the screen saver is using these -lock and -unlock methods internally to control access to General/OpenGL code.


It turns out these assumptions are true for the most popular General/OpenGL modules (ahem...) and Assimilator can effectively guarantee no-one else will be monkeying around in that module's General/OpenGLContext while it is trying to copy the context's pixels. Multithreaded modules that don't conform to these assumptions will panic when running under Assimilator, but perhaps the Assimilator author can convince others to use the conventions outlined above.

So this illustrates one way this problem has been solved: subclass General/NSOpenGLView, add an General/NSLock to the class, expose API to lock & unlock the General/NSLock, and use those methods whenever trying to send General/OpenGL data to that view. I think it qualifies as "otherwise global" from your problem statement. You might also try the same strategy with General/NSOpenGLContext's: subclass them, add a lock, add some API, and don't forget to use it! But stashing the lock in the view seems reasonably convenient, IMHO. Or you can use a global, or a singleton class, but both of those strategies seem sloppy to me.

Finally, as I have said before, you will still need to solve this problem in fullscreen mode. This "panic problem" isn't caused by General/NSOpenGLView and General/AppKit, it's an underlying restriction in General/OpenGL. It doesn't matter if you're using Cocoa, Carbon, or Object Oriented Fortran, you can't safely send GL commands to the same context from different threads.

-- General/MikeTrent

----

Okay, thank you. As it happens, I agree regarding the global/singleton class; it would be sloppy. The subclassing solution would work for a fullscreen General/NSOpenGLContext also, and I might use that to hold the lock for General/OpenGl when working between my General/NSResponder subclass and General/OpenGl with multiple threads.

Anyhow, thanks again, Mike! Good solid info, all around.

-- General/RobRix

----

While it is not available on-line, the January issue of General/MacTech has a great Cocoa/General/OpenGL primer by David Trevas. I posted my "General/OpenGLWithCocoa" project file at http://homepage.mac.com/sashag/FileSharing4.html 

It is mildly commented, but may be useful without the article.

-- General/SashaGelbart

----

I have added a small General/CocoaOpenGl sample which shows how easy it is to use any old General/NSView subclass as an General/OpenGL drawable: General/CustomGLView.

I also have a fullscreen to windowed and back again sample here:

http://homepage.mac.com/brentgulanowski

-- General/BrentGulanowski


----

I have a number of years programming General/OpenGL (and SGL before that) on a number of platforms. I'm new to General/MacOSX/Objective-C/Cocoa. Nevertheless, I have managed to put together a number of simple examples that seem to work OK.
I'm looking for a site that I can upload to.

jimd

----

I've downloaded the Modeller sample code from Apple (http://developer.apple.com/samplecode/Modeller/Modeller.html) and it compiles just fine. However, when I run it, it won't bring up a new document window. It seems like it's not getting a frameName for the currentFrame (there's an assertion failure in General/NSCell for getStringValue. I'm just a hobbyist playing with General/OpenGL, so I'm not sure which rabbit hole to chase down. It feels like there's some problem initializing the General/OpenGL view from the nib, but that's about as far as I get. This is with PB 2.1 (Dec 2002 Dev Tools) on 10.2.8. Has anyone else tried this recently?

General/GKinnel

----

Yes, I just downloaded it, (and remembered that I had already tried it another time, too), and I got the same thing. I know this reply comes about a year late, but perhaps this will reopen the discussion? Anyone know how to fix this?

-- General/JesseAbram

----

Fix the assertion failure and you should be good. An assertion failure throws an exception. If nobody's watching for it to catch it, it will effectively abort the setup process for the window, and so nothing will appear.
