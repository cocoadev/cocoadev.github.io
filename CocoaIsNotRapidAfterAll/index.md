---
layout: page
title: CocoaIsNotRapidAfterAll
---



Brief history... I have been an application developer for about 15 years now (two of which were spent actually working at Apple). I am tired of doing the same things over and over. I am excited about the latest UI development frameworks. I was hoping that Cocoa would be one of these. It still has lots of problems... 

 I have spent the last year developing a rich media application in WPF. I can't really say more than that. It has a lot in in common with Apple TV. Go figure. I hate that I get up every morning and write code on a Windows machine - then go home and play with my Mac all night. The truth is that I honestly believe that although Apple has won the OS battle (in the sense that OS X is just awesome), Microsoft is winning the development tools battle. And I hate that! I want to do something about this but don't know how!

I have made some posts in various forums - not really knowing where to post to get the most help, or actually what to say, but here is a brief history if you are interested in reading:

http://www.idevapps.com/forum/showthread.php?t=6179

and the latest one:

http://discussions.apple.com/thread.jspa?threadID=1122687&tstart=0


Obviously I am fascinated by the concept of resolution independent UI's. This is the future. Unfortunately Cocoa does not quite seem to be there yet - as a resolution independent gui implies that everything must be vector based (even the images in the toolbar buttons).

Ok. Enough broad soapbox ranting. Here are some specific topics/issues I would like to understand about cocoa:

1. Is it possible to import an Adobe Illustrator image and render this an NSBezierPath?

I can do this without a single line of code in WPF! 

2. Gradients are becoming an important part of UI design. Why are they not part of Cocoa? And why are they so difficult?

2.a It is my belief that there should be a color picker and gradient designer in Interface Builder - which allows you to define the background color/gradient of any NSView. This is just too obvious.

3. NSView needs 2 very important properties/attributes - whatever you want to call them. CornerRadius and Backround brush. Rounded rects are in-vogue and not going anywhere anytime soon (check out all of the Mac Pro Apps). Not only that but each corner needs its own radius - or lack there of. So cornerRadius should be a typedef with topLeft, topRight, bottomLeft, bottomRight.  Yes, I am starting the development of my own superhero NSView and will publish it when I feel comfortable, but I don't feel that this should require extra development. 

4. I find it retarded that Apple keeps "unpaid" developers out of the loop of upcoming releases. Of course I didn't think this way when I worked for companies that were part of the Apple Premier Membership - however to feed the wound - MS offers CPT builds of future platforms to anyone interested. just saying - because I can't comment if any of this is addressed in Leopard.

Thanks.

-johnny

----
At first I was inclined to dismiss this as a troll/flame/spam post, even though you say that you're on "our side". However, you do raise a few points that bear discussion. (However, if other members of this site consider this a bit too MailingListMode, and nominate it for deletion, I can't exactly say they're in the wrong.)

So, point 1: An Illustrator image is just that, an image, whereas a bezier path is a single path/shape that corresponds to one shape within the Illustrator file. (My terminology may be a bit off; I'm not really an Illustrator user.) Therefore, the class you *would* use is NSImage, but NSImage does not seem to support     .ai Illustrator files. Remember, though, that you can save Illustrator files as (supposedly lossless) Illustrator-editable PDFs, which NSImage supports just fine.

Point 2: Gradients should be easier. A few places (like http://www.gradientpanel.com/ ) are making strides in the right direction but still no real native support. Background *colors*, however, are not really "Mac-like" these days, at least not for anything outside of the standard control colors. (The only views I can think of with background colors are the side-panel master-detail tables in iTunes, Mail, and the Leopard Finder.) Even so, a subclass of NSView could implement this (such as Dave Batton's DBB<nowiki/>ackgroundView), but I wouldn't burden every NSView, most of which are generally transparent, with this sort of drawing code.

Point 3: Again, very few NSV<nowiki/>iews actually need a rounded corner: not NSTableView, not NSTextView, pretty much just NSButton. Now, custom styles of button are useful, perhaps, but most of the time a plain old rounded Aqua button is all you need. The floating HUD windows (like the one in Motion) are a different story, but they've already been reproduced quite well by the developer community. Really, though, the point to remember is that an NSView's bounds are the field it *can* draw, not necessarily the field it *does* draw. A view that draws rounded corners can still "own" the undrawn remainder, especially since computers handle rectangles a lot easier than curves. (And to forestall the "power of today's computers" argument, I'm including complexity of code in that objection.)

Point 4: Closest to troll. Yes, it sucks, but it does seem to work as a business strategy, and it allows Apple to keep changing their APIs without harming "everyone". Also, I'm not familiar with Windows development, but is what they're making publicly available during development actually similar to Cocoa? Or is it closer to CoreFoundation? (Just a question.)

I'm not going to post again for the simple reason that that would definitely remove community value. Let's get someone else's thoughts on this too. Still, I would like to close by saying that at least in theory, Cocoa applications are supposed to look and feel similar, and there are a large variety of tools and existing classes to help you do that. The fact that Apple doesn't provide the classes they use for their own apps is rather bothersome and closer to the core issue, but that's nothing new. Fortunately, many talented developers before you and me have re-solved many of these new UI elements and put them up for public use, if you just search in the right places. --JediKnil

----

I appologize for any trollness that came across. I truly am here to learn how to develop for the Mac (preferably using Cocoa / Objective-C) - and I think there is enough usefull information in your reply to justify keeping this alive. 

Some quick replies... An illustrator file is simply a collection of paths. These paths can be grouped in groups and layers. In the XAML exporter (tool used to export     .ai to     .xaml ), you have the option to use this entire collection as an "image", or you can access each individual path. I am only interested in displaying the entire "image" - so if NSImage can import vector PDF's then this is exactly what I am looking for. I am sure there are some features of Illustrator which probably don't play well with PDF - like layer effects - so this will require some playing around with to see what works and what doesn't. I will report what I find.

I think the move towards resolution independent design is important in the growth of application design - and vector based images are the key to making it work.

The GradientPanel looks nice. Thanks for the link.

I would agree that most Cocoa-like apps don't have a need for rounded corners. However, if you consider a UI along the lines of one of Apple's Pro-Apps, then you do see a lot more use of rounded containers - other than buttons. Aperture is a prime example. What is the suggested path for developing something with a more Pro-App look and feel? Customize Cocoa or develop your own widget/control library in Carbon?

As for Windows developer relations, early releases (alpha) are usually reserved for paying MSDN subscribers. However for big projects like the WPF framework (which is similar to Cocoa in scope and functionality) - there is usually a large open public beta cycle - with the entire suite of developer tools available. But I know how secretive Apple plays - and thats just the way they do it. I guess if you want in on the action, you just need to fork up the $500. 

--johnny

----

So you can export .ai to XAML in WPF?  Why bother with something similar on the Mac when the entire Quartz system is based on Display PDF?

----

Cocoa's drawing system is actually pretty easy to use compared to .NET. Gradients and rounded corners may not be the easiest things for a beginner, but once you find the answer (which won't take long at all through Google) you can just stick it into your own library or class addition and not have to worry about it again. I can't really think of too many situations where you would use either one without subclassing NSView or another control for custom drawing anyway, so it's not a big loss that it's not available in NSView itself. The same goes for background colors and other drawing techniques as well.

--MarcCharbonneau

----

So yes, JediKnil, using a vector image in Cocoa (from Adobe Illustrator for instance) is as simple as saving it as an editable PDF. Thanks for the tip. This seemed to work flawlessly in an NSImageView or ImageWell - even with some Illustrator layer effects applied to the image. I didn't try to get too crazy - I am happy with what is working for now.

However I did come across one thing I couldn't figure out. If you are using the image inside of a button, how to do you scale the image to fit the dimensions of the button - preferably scaling it proportionally? NSImageView has the appropriate scaling attributes available in the attribute editor. I was expecting to find the same thing in the NSButton attribute editor, but could not figure out how to do this. I don't really want to scale the image prior to converting to a PDF - as this doesn't really make sense anyway when using vector images. They have no "inherent" size anyway.

I will whole-heartedly agree that Cocoa is leaps and bounds better than typical .Net - like MFC, WinForms, .Net 2.0 etc... That stuff is pure crap! However there are some truly elegant things about WPF - but this forum isn't about WPF... 

I played around with the GradientPanel a bit tonight, and its pretty slick - definitely solves the gradient problem for cocoa. And its also not that difficult to create a border subclass of NSView. The one I made allows a you to set a different corner radius for each of the 4 corners. Fun!

So please let me retract my statement that Cocoa is not there yet - It obviously has its foundations in vector drawing and resolution independence, I guess the problem is that the editing of these core objects (NSBezierPath, NSRect, NSFrameRect, et. al)  are not built into IB - Also, I see very few people actually using things like vector based icons, button images and UI adornments in demo applications. I personally like them much better than their bitmapped counterparts. But maybe this is just a personal preference. 

-johnny
----
It seems more like you didn't read the documentation properly and do a little bit of google and cocoadev research before complaining about cocoa's shortfalls. I think you may have jumped the gun on calling cocoa an underdeveloped API. A few simple google searches would have revealed the results you needed to solve points 1 - 3. I'm not trying to defend Cocoa, because it isn't a perfect API - but your complaints were just plain ignorant.


----

*I guess the problem is that the editing of these core objects (NSBezierPath, NSRect, NSFrameRect, et. al)  are not built into IB*

This is far more difficult a request to 'get right' than you realize. Check out the recent thread on the cocoa-dev mailing list (check the archives) regarding PICT support for the myriad reasons PDF editing is non-trivial.

----

This is a question of scope.  Personally, I side with Cocoa having a limited scope compared to a scripting language like Python.  Cocoa provides all of the basic tools that I need to build bigger components.  That is what it is designed to do and I think it does it well.  It doesn't make sense to me to attack Cocoa because it doesn't have all of the features of another framework in the same way that it doesn't make sense to attack a sink for not being a dishwasher. -G

----

*It seems more like you didn't read the documentation properly and do a little bit of google and cocoadev research before complaining about cocoa's shortfalls. I think you may have jumped the gun on calling cocoa an underdeveloped API. A few simple google searches would have revealed the results you needed to solve points 1 - 3. I'm not trying to defend Cocoa, because it isn't a perfect API - but your complaints were just plain ignorant.*

Damn - thats pretty harsh. I will respond appropriately and leave you to your eliteness.

I think what I did was misrepresent what I was meaning to say. I never really meant to imply that Cocoa is an under developed API. It has always received high marks from developers.  I think the main issue I have is actually with Interface Builder. It is not really much of an "Interface Builder". It seems fit for hooking up messages / controllers, etc, but as far as aiding in the design of an Interface, its not there yet - try adding a toolbar using Interface Builder! Which is why I titled this as CocoaIsNotRapidAfterAll - and not C<nowiki/>ocoaIsAnUnderdevelopedFramework.

Note - I recently had the opportunity to play around with XCode 3 - and IB seems to have recieved much love in this version. Seems like at least Apple developers were thinking along the same lines as me. Interface Builder should allow you to "Build Interfaces". I also noticed that in XCode 3 - there is now the capability to bind the dimensions of the image used in a Button to to the size of the button - which seems to be a missing feature in XCode 2 - or at least one that is not documented or obvious.

For point #1, I did do some google searches. Didn't come up with much. It doesn't seem like people use vector images very much in cocoa UI design which is maybe the reason. I see mostly .png and .tiff files used to build up UI elements. 

For point #2 I have actually not found a reasonable answer. I have found a reasonable implementation - indeed - but my original question is why are gradients not part of the Cocoa Framework in an easy to use method? I think most people would agree that gradients in Cocoa/CG are obtuse.

For point #3 - I understand now that NSView is meant to be an empty content template - with which you can subclass till your hearts content. Got it. 

And finally, in response to *This is far more difficult a request to 'get right' than you realize. Check out the recent thread on the cocoa-dev mailing list (check the archives) regarding PICT support for the myriad reasons PDF editing is non-trivial.*

Again, I think I was misunderstood - surely my fault for not being clear. I am not really interested in editing PDF's that have been imported into my app and used at runtime. What I am more interested in is a way to create NSBezierPath objects in a visual manner. Again, this seems like something obvious to include in Interface Builder. A simple pen tool a-la illustrator and the ability to apply stroke and fill properties to these objects would be really cool. Thats all - it just seems rather archaic and rather inefficient to build complex bezier objects in code. This tool is available in Blend - which is WPF's version of Interface Builder. If I need to create nice looking vector play button, or ffwd button and even add the ubiquitous glass effect for example, I can do it all right inside the dev environment - no need to fire up photoshop/illustrator and add external resources to the project.

Thanks for those who responded with help. It is appreciated.

-johnny

----

With respect, I think a lot of the argument has been in response to your definition of "rapid".  Cocoa doesn't do everything that you think it should and you say that it isn't rapid.  I would argue that Cocoa provides more than enough to be considered a RAD.  I'm certainly more productive using Cocoa and the associated frameworks and development applications than in any other environment (while admitting that I prefer TextMate to Xcode's text editor).  If you think there should be a bezier drawing tool in IB, that's one thing (and I admit that IB could use a few more tools), but I don't think you could possibly argue that Foundation, AppKit, Core Data, Core Image, WebKit, and the other associated technologies don't allow for the rapid creation of applications. -G

----
No eliteness present here I don't think. Just remember that when you construct a window with a table, a view and a few buttons in it that you better be damn glad that you don't have to write ALL of the code that was written by Apple developers when they were building the frameworks. You've missed the boat in why cocoa is rapid after all, because it allows you to get on with coding the purpose of your app rather than spending the time re-inventing the wheel.

----

Some of your complaints are addressed in "a future version of Mac OS X".  For example, creating toolbars in IB might be fixed.  And the whole resolution independent UI push is going to force a lot of drawing that used to depend on external bitmap resources to be drawn using bezier paths.  I'm sure Apple's UI team hates hand-coding paths as well.

Besides, Cocoa isn't RAD in the sense that VB6 was RAD.  A lot of Cocoa is more rapid by its very nature than the equivalent in, say, MFC (remember, Cocoa has been around for a *very* long time... how old is WPF?).  Remember toolbar strip bitmaps?  And how you used to grind your teeth and grumble, "Why the hell won't a common control accept an ImageList?"  I sure do.  And I'm not missing it one bit.

----

Mind your NDA. Some of the information you've given is not public.

----

FYI, I am a programmer by day and a musician by night - and it delights me to see that the latest version of Logic has inherited the entire Aperture "GUI Framework" - as it was the Aperture look and feel that got me started in this entire fascination with Cocoa development in the first place - as I happen to be very fond of this style UI - even though I am not even sure if this Aperture "Framework" is Cocoa based or Carbon based. However, Cocoa is the future of Mac programming, so this is direction I will head (and there are .nib files in the Logic 8.0 resources folder so is that enough to assume it is Cocoa based?)

I was not meaning to be a hit-and-run poser. I have actually been starting from the beginning so-to-speak for Cocoa development - I am currently studying "Cocoa Programming for Mac OS X (Aaron Hillegass)" - and am really trying to learn the Cocoa way of things. However the previous poster brings up a really good point. Cocoa is not a "modern" framework. It has been around since the days of NEXT. Being a graphically oriented developer, it still bothers me to no end to open up my favorite app's resources folder and see an endless sea of .tiff files - especially ones that are named image.left, image.middle, image.right.  I think that is the basis of my entire rant. With Apple and OS X leading the way of the future (at least in terms of usability, robustness, interface design) - there needs to be a step in the direction of losing the reliance on hard-coded bitmapped images. 

I hope I don't come across as a poser. I am currently developing a major application for one of the top 4 "media" companies in the internet world. The entire GUI is vector based and implemented using 2d hardware acceleration. This is important. What happens when monitor manufacturers start using higher DPI screens like the iPhone (160 dpi or greater). What if we want the current apps to look the same on these monitors? Do we now have to re-release 160 dpi version of the app?

If anyone is interested - I have found a really nice site which explores many of the core differences between Cocoa and WPF - mostly leaning towards Cocoa winning most of the challenges. If you are interested in these sorts of things, here are are view articles:


http://dotnetaddict.dotnetdevelopersjournal.com/nib_vs_xaml.htm

http://dotnetaddict.dotnetdevelopersjournal.com/cocoa_wpf_bindings.htm

http://dotnetaddict.dotnetdevelopersjournal.com/leopard_techtalk.htm

http://dotnetaddict.dotnetdevelopersjournal.com/instantiate_runtime.htm



This is some pretty cool stuff! Apparently the Cocoa binding model is really nice. I just really wish I could design my UI's without having to use button.left.png, button.middle.png, buton.right.png. Those days should be over!

-johnny

----
First and foremost, I think Apple (rightly or wrongly) encourages consistent use of the standard user interface elements.  There isn't a lot of need to provide custom vector drawing in the GUI design tool if you are using the satndard components.

Second, even if you were going to draw unique looking user interfaces for every application, you wouldn't want some half-assed vector drawing tool to do it.  You would want professional level tools of at least the caliber of Adobe Illustrator or Corel Draw.

Third, everyone knows that Apple tossed out the user interface guidelines and is producing non-standard interfaces left and right.  Apple said that they would revert to a more standard interface in Leopard, but Apple has never been good at keeping promises.  When Apple does non-standard things, third parties try to either replicate the latest Apple experiment or just assume that all guidelines are obsolete and do their own unique thing.  It's not the frameworks' fault if third parities produce bad non-standard interfaces except to the extent that the frameworks could provide technology that helps third parties avoid stupid mistakes.

Finally, I think you will find that every place Cocoa uses an image, it will happily use a vector format.  Cocoa and the NSImage (previously called Image) class have supported ubiquitous resoulution independant vector based user interface design since 1988.

See NSImage's
usesEPSOnResolutionMismatch
Returns a Boolean value indicating whether EPS representations are preferred when no other representations match the resolution of the device.

- (BOOL)usesEPSOnResolutionMismatch

If you build your user interface with vector based images, Cocoa will draw your user interface with vector based images and will always choose the best image representation to match the resolution of the output device.

EPS in this case means Encapsulated Postscript, and in modern days it means PDF.
See http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaDrawingGuide/Images/chapter_7_section_3.html


----

*"I just really wish I could design my UI's without having to use button.left.png, button.middle.png, buton.right.png. Those days should be over!"*

This assertion is based on misinformation at best. The only reason those days aren't over is because you're ignorant of the correct way to do things. Gradients may be harder than they need to be but there's plenty of very simple example code (released in the public domain) that shows how to fill a bezier path (or a simple rectangle) with a gradient. It's *easy* if you take the time to read, understand, and apply the drawing-related API in Cocoa and simply drop in a gradient-slinging convenience class (such as CTGradient, found in 0.3 seconds via Google). 

If you would reinvest the time it takes to make these misinformed "impossible-to" drive-bys into researching "how-to" instead, you'd know better.

Further, the entire premise of this page is ridiculous given that, if you use *any* "rapid application development" environment in the way it was intended, it's "rapid". It's when you want to do something non-standard that you have to do extra work. For example, if you follow the HIG (as a poster mentioned above) building a fully-functional and fairly complicated app can be done entirely through drag and drop using the standard UI. That's  *incredibly rapid* in my opinion. If I want a pretty gradient bevel on the bottom of my window with a little "LED" that indicates some state (ie, "nonstandard") then I have to make my own ... then drag and drop it where I want it from that point on. My point is that it wouldn't surprise me if those who complain that Cocoa is "not rapid after all" when building beautiful, custom controls were to also never play the lottery and complain they never win. *Some* effort is involved.

