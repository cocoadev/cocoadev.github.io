---
layout: page
title: GlobalModifications
---



Nostalgic for windowshade from MacOS 9? well, beats me how to get it systemwide**[1]**

----

[1] Enabling this system wide would involve writing an NSWindow subclass; then, using poseAs to have your window subclass pose as, you guessed it, NSWindow. You would need to make your window subclass into a framework and install it in say /Library/Frameworks; then, on your next login, if you've done things correctly, your subclass should be working system wide for Cocoa apps. **NOTE:** I am not suggesting that anyone actually do this or that it is easy; just that it can be done.

-- DaveHenderson

This, as pointed out below, is not sufficient since the apps you are trying to modify the behaviour of do not load your framework.

-- David Remahl

Thanks for the clarification :). I was not by any means claiming to be an authority on this subject. Just pointing out that it is possible to globally modify the Cocoa frameworks thru the 'pose as' mechanism. Has anyone else, noticed how many David's commented on this subject? ;)

-- DaveHenderson
----

I never managed to get the poseAs stuff to work sys wide...

-- David

----

Aside from anything else wouldn't it require the app using it links against your framework?

-- FinlayDobbie

----

Not necessarily - there's an older MacTech article describing how to change window colors system-wide in Rhapsody. I figure the basic techniques still apply in OS X, but they're not for the faint of heart, because they require substituting your framework for the AppKit. 

If you're interested, here it is:

http://www.mactech.com/articles/mactech/Vol.14/14.03/RhapsodyinPurple/

-- JensBaumeister

----

Well, actually the trick **is** to make all applications link to your framework, as described in the MacTech article.

----

Thanks a lot! I tried doing something like that, but missed a few vital steps. I'm looking forward to coming home and play with it!

-- David

----

Just make sure you remember how to boot into single user mode and get everything back to normal in case something goes wrong. :-)

I'd especially double-check the parameters for your own framework (version number etc.) since they have to match the AppKit's, and I'm sure those will have changed sice 1997.

-- JensBaumeister

----

Haha. I actually had to do that many times when I was experimenting with it before. Now I'm more confident on how to do it for real.

-- David Remahl

----
I went home and played with the MacTech stuff and actually managed to augement the NSUserDefaults class to first write to the console the lookup key before sending the result back to the requesting application.

You wouldn't believe the amount of NSUserDefaults requests are made!

If you wan't to read more, I moved this topic to a new topic: GlobalModifications.

----

Here is the instructions for:

Making all applications' requests for NSUserDefaults keys be written to the console.

0 ) Become root in the terminal (su or sudo)

1 ) In the terminal, write:
    cd /System/Library/Frameworks

2 ) type:
    mkdir OldFoundat.framework
Make sure the name you choose for the folder is as long as the string "Foundation.framework", or you'll run into problems later.

3 ) now:
    (cd Foundation.framework; gnutar -cf - . ) | ( cd OldFoundat.framework; gnutar -xf - )

4 )      cd OldFoundat.framework

5 )      cd  Versions/Current
     cp Foundation OldFoundat

6 )      ln -s Versions/Current/OldFoundat OldFoundat

7 ) (this is where it gets shaky...)     otool -L OldFoundat

8 ) Write down the first path (that to the Foundation framework file) including the version numbers!

9 ) Make sure TextEdit is not running, and type:
    open -a TextEdit.app OldFoundat

10 ) Scroll down (or search for) the path you wrote down on paper and replace the two occurances of Foundation in that pathname with OldFoundat.

11 ) Fire up PB and create a new Cocoa Framework project. Name it Foundation.framework.

12 ) Remove the automatically added frameworks "Foundation.framework" and "AppKit.framework" form your project.

13 ) Add the OldFoundat.framework to your project.

13 ) Go into target settings and set the v-num and compatible vnum and C for the revision letter to match those of Foundation.framework (you remember, those you put down on paper.)

14 ) Create a new class in PB and make it a subclass of NSUserDefaults. Call it something catchy such as ConcealedInformantNSUD ;-)

15 ) In the implementation, override the +load method:
    + (void)load
{
	[ConcealedInformantNSUD poseAsClass:[NSUserDefaults class]];
}

16 ) Also override the objectForString: method.
    - (id)objectForString:(NSString*)string
{
	id obj = [super objectForString:string];
	NSLog(@"%@ -> %@",string, [obj description]);
	return obj;
}

17 ) Next, build your framework. If it builds cleanely, great! If not, either you or me messed up. I typed the above code dicrectly into the browser, so you may want to check it. Fix it if you find a problem.

18 ) Now it is time to replace the original foundation with your own.
    cd /System/Library/Frameworks
cp -r /Users/yourusername/thepathtothebuiltframework/Foundation.framework NewFoundat.framework
mkdir backup
mv Foundation.framework backup
ln -s NewFoundat.framework Foundation.framework

19 ) Change some pointers:
    cd /System/Library/Frameworks/NewFoundat.framework
rm -r Resources
ln -s ../OldFoundat.framework/Resources Resources

20 ) Reboot! The next time you boot, your system will either
- Not boot at all...In that case, reboot pressing down cmd+s and use all your knowledge to reverse what you just did!
- Or: Boot as normal. Open Console.app and see *every* request a Cocoa app does towards NSUserDefaults! I for example found a key to change the font for the TOC in Mail.app and lots and lots of even more interesting stuff ;)

21 ) More to do:
- I am sure you can come up with other stuff to do with this technique. Post suggestions and results here!

-- David Remahl (the above is greatly influenced by the mactech article. You may want to refer to that regarding risks etc...

----

Great!  I've got code for windowshading in Cocoa that I wrote a few weeks ago and will post it here later today... it's as a NSWindow subclass so it should be pretty simple to adapt.

-- Nicholas Riley

----

Yes, the proceedure is portable to AppKit as well...Note however that this is one of the hackiest solutions one can think of. It would be an absolute impossibility to ever distribute anything like this. Remember that. But it sure is a *fun* way to play with and customize your system!

-- David

----

Well, no need to post all the WindowShade code here, since you can find the whole deal in the WindowShade page already.  :)  Just click on WindowShade.

And.. hmm..  I can see someone feeling evil writing a lil utility that automates this whole process so that 15% of us can happily customize our systems, while the other 85% reinstall immediately after using it.  Either that, or Apple absolutely going bonkers and sending the hounds after the author.  Talk about a Customer Support nightmare.. heeheehee

I think I'll get started on that right away...

-- LeslieOrchard

----

No, please stay away from it...I assume you were kidding, but doing that would be a real hassle...

-- David

(Oh, most certainly I was kidding.  This sort of thing is kinda like hooking a nitro kit up to your car.  Could get very messy if you don't know what you're doing.  -- LeslieOrchard)

----

Idea:

In the MacTech article, they briefly outlined a similar hack that used hack-bundles which were loaded at runtime.

Wouldn't it be possible to patch the FoundationKit with this hack in such a way that one wouldn't have to re-compile the substitue framework for every new patch but instead just add some code (to NSObject, for example) that would allow to load any subsequent patches into memory at runtime?

-- JensBaumeister

----

Please describe what you mean in a bit more detail...Do you mean that the patch framework should load bundles containing additional patches? Sure, I am confident that can be done. Which means: if everyone agrees on a plugin standard, we could have extension-like support, once we go through the somewhat painful first patch.

I am still not in favour of distributing anything like this to a broader audience...

/ david

----

Yes, that's pretty much what I meant. And no, I wouldn't want to distribute this beyond those who know how to patch it themselves, it'd only cause problems and people would hate us for trashing their systems. 

But for those who know how to write their own patches: If you could load additional patches as bundles, at least you wouldn't have to mess with System-level frameworks every time you think of something new to patch. (If a loaded patch kills the system, at least you would only need a simple reboot to fix things and change your code, no single user mode required.)

-- JensBaumeister

----

Yup. In fact, reading the MacTech article closer, I see that his MacHack did smtng like that. I shall write some kind of graphical bundle manager tonight and keep you posted.

The mods should load as early on as possibe, so that as many programs as possible are affected. Maybe the loaded ones are saved to prefs, but only temporarily until there was a successful boot-run-shutdownnormally, so that a crasher would get automatically disabled if it appears it made a full boot possible...Just an idea...I'll think some more...

-- DavidRemahl

----

I"m working on a project with a friend called InsanityX which aims to add a few "missing" widgets to the title bars of applications. Like an alpha value controller, extra windowshade widget, and possibly some fun things with CoreGraphics (we got a few weird effects the other day, like spinning a window around and placing it back-to-front, but couldn't fully grok how the matrix was affecting them). And perhaps just a randomness function that would make things spin insanely and move titlebar buttons around and other strange things...

Damnit, this would make a leet hack for MacHack! Anyways, what do you guys think? Currently I've just added a blue blob to the titlebar that will set the alphaValue of the window containing it to 0.50, and this is just being implemented in a .app, but hopefully in such a way that it will be possible to rip it out later and make it system wide! :)

-- FinlayDobbie

----

Unbelievable! I made the exact same thing, about a slider setting xparency from the titlebar the other day! It is indeed possible to make sys wide.

I think we should all agree on a spec for the plugin architecture. I will type up a protocol definition later today that will allow plugins in /Library/Modifications/ with extension .*mod load automatically. Then we'll write code to make a modified Foundation and a modified AppKit load those. We could add a plist key to the plugins specifying if they want to be loaded together with the AppKit or with the Foundation.

I'll post whatever I come up with here. It would be nice if we could agree on something so that we easily can try each others mods out.

-- DavidRemahl

----

Sorry it took a while, been busy.  Here's my CocoaShade version:

[Moved over to WindowShade page]

--Nicholas Riley

----
I haven't yet managed to get the frameworky thing working yet, I'm not quite sure why. One thing I've discovered, however, is that, at least with AppKit, you can replace it and test the results without rebooting or even logging out. After you've soft-linked your framework to AppKit, you won't be able to launch any new applications if it was done improperly. Old ones work fine, though. You can then easily re-link to the old framework, and everything works again. Wonderful.

That's all,
-DavidAdamson.

----
Very interesting article, I've learned lots of things. May I ask quite a lame question: does all of this work with carbon apps by any ridiculous chance? If not, is there an alternative method to get this working with carbon apps?

-AndyPark

----

This particular hack won't work with Carbon, because it uses some Cocoa-specific features; namely the ability to let a subclass pose as its superclass.

(**Q** : Is that a Cocoa-only feature or is it an integral part of ObjC?)

Since I never really touched Carbon (other than for the occasional call to QuickTime functions), I don't really know if you can get anything similar to work there.

-- JensBaumeister

----

There was a hack at MacHack that did windowshade in Carbon by replacing Carbon.framework or something similar... It doesn't involve poseAs: but instead requires overriding symbols, I think.

-- FinlayDobbie

