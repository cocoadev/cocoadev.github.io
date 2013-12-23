---
layout: page
title: OSXTiger
---

OSXTiger 

I think that since Tiger will be released soon, be outta start converting this page over to actually having information abut OS 10.4 Tiger. For example have an area describing Spotlight, Core Data, Core Image, etc... for now of course, we could only post stuff that Apple has on their main Tiger pages (ie. not from within the ADC website or pre-release builds), and then could expand on it after release. **(yea:2 nay:0)** http://goo.gl/OeSCu






----

So what features from a developers point of view will be anounced? ApplescriptStudio++? YCode?  Stripe Oriented Programming?

*Some preliminary rumors from AppleInsider:* http://www.appleinsider.com/article.php?id=473

----

What is Stripe Oriented Programming?
**It's a joke. A Tiger has stripes!** 

*There's a case for Stripe Oriented Programming at BetterArticleTextLayout ;) And shouldn't it be AppleScriptStudio = AppleScriptStudio +1?*

Shouldn't it really be "set AppleScriptStudio to AppleScriptStudio + 1"?

Too long of a line for me.  I don't like ApplescriptStudio that much. -RossDude

 *it should be **set AppleScriptStudio to (AppleScriptStudio +1 as number)***

See?  AppleScript is too stupid for me.  How does anybody work with it???????? -RossDude

Uh no, it should be "    set AppleScriptStudio to (AppleScriptStudio + 1 as interfaceFramework)" or whatever you want to call it (it's more of a bridge, I guess). --JediKnil

----

As of today, April 12, the official release date for Tiger is Friday, April 29!
    
set theList to every application whose API is "AppleScriptStudio"
repeat with eachApplication in theList
    tell eachApplication to quit
end repeat


----

No seriously: how about that Mono bridge Quark is working on, so cocoa programming with C#(blech), VB etc.

Cocoa + C#!? Kick ass. Obj-C has some coolnesses (dynamism, named arguments, a few others), but I've heard some interesting stuff about C#. I'd love to try it out.

My glances at C# lead me to believe it takes Java and added back in PILES of just STUFF... structs (psuedo classes that are given a different memory location for speed), enums (ok not so bad..), "delegates" (function pointers), "events" (sets of "delegates" that get called when the event is called... a meta-function?), overloaded operators, namespaces, genericity (coming soon), argument lists (as in variable length argument lists like NSLog), and so on and so on... If you thought Java's type declarations were too long, you ain't seen nothin' yet.  It's a very BIG superset of C anyways... What is the main complaint against C++ again... too big... hmmm yup applies here.

A Mono package is available from [http://www.go-mono.org/download.html].  I installed it and tried a few
small programs---it seemed to work for them.  There are a number of other examples on the site, some
using GTK.  See [http://knowledgebooks.com:8080//space/start/2004-05-10/5#Running_Mono_under_OS_X]
for information about setting the path.

----
KVC is still very immature; I'd like to see it grow up a bit.  More often than not, if you're doing anything that has an even semi complex gui, controllers, controllers and more hand coded controllers is what you'll be needing.

KVC???   What's KVC?  **Ooops -- I meant Key Value Binding  -- or in other words. KVC+KVO **


*KVC ~ KeyValueCoding
*KVO ~ KeyValueObserving
*KVV ~ KeyValueValidation


Amen to that.

----

Yes, I'd like to be able to do more binding oriented hings without the need for some many controllers.  For example, bind the enabled attribute of a text field to a check box.  Maybe one shared controller to handle simple things such as this?

----

A Finder that works would be nice too.

A Finder that is actually multithreaded; my entire Finder hangs whenever I try and access some network mount over a slow connection.  BeOS had an amazing setup; every window in their Finder equivelant file browser had two threads, one for the window (which isn't necessary given the way the window manager works in OS X, and one for the processes occuring in that window, which is what the OS X Finder needs **really fricken' badly**.  It's not a big deal code wise either (If I were a betting man, I'd say no more than 700 lines of code would need to be changed).  I don't understand why Apple hasn't threaded the primary interface to the rest of the computer properly.

----

Speaking of Finder and BeOS in the same breath... let's get BeFS support into the mac... I mean a nice clean way to add arbitrary metadata to a file and sort/find/act on it in the Finder... I mean we are using a file system with a resource fork are we not... might as well actually use it for something good.  Besides, if they did this we'd get almost the entire benefit that MS hopes to get from WinFS without having to debug it until 2009. * I can verify that metadata is making a big comeback in 10.4 *

----

How about bundling a SQLlite or SQLite-like database with a nice Cocoa API to go along with it.

*How about bringing back EOF so we can use any database we want?*

I already bitched about this; but hot jiggidy; I have a folder in ~/Documents/ tha contains all my iChats... like many other people.  There are a lot of iChats in there.  My whole Finder freezes up with the SPOD until it displays all the contents in that dir... some threads would be nice...

----

What about Declarative User Interface Programming like MS XAML, Mozilla's XUL or GNUStep/Cocoa Renaissance. Apple is already doing it with the iTunes software. The web app server returns an XML file containing data along an XML markup that is interpreted by the iTunes app to render the UI using Cocoa widgets. Might Apple open up this to us? - *iTunes is a carbon app, and therefore it using Carbon widgets*

----

The Finder is unbearable, especially now with the hacks since Jaguar (ie. labels, brushed metal, sidebar -- bugs galore)... I quit it and keep it closed 90% of the time! <-- Um... you can hide brushed metal and the sidebar, it's not very buggy, and labels have been a part of the Mac OS for years, they were just not brought back until Jaguar.

Um... hiding the brushed metal also hides everything else, including the toolbar, that is unacceptable. It contantly locks up, displays icons incorrectly (corrupt also) and is plain slow (see above for examples). The way the labels are done is very poor; bad selection widget in the menu and the way the labels are displayed is not very appealing at all. The Preferences dialog can't even remember it's position. The sidebar is full of bugs. The brushed metal used in it is non-standard, its some sort of a hack, not using the OS graphics. The "Open With" menu sometimes does not feel like showing up.

----

I've referred this page to Mac OS X Feedback.

----
I want to have XGrid integrated in the OS:

*
XGrid pref pane being part of the default set, so you don't have to ask people to install it to have them on your cluster
*
A Cocoa framework that allows one to have Xgrid integrated inside any application, and not just a plug-in architecture
*
An enhanced version of the 'Create your custom plug-in', which is very limited right now


I hope that would be optional, most users do not need this.

*I think it is actually quite the opposite! If a framework is added, it will allow developer to include Xgrid capabilities to any app, and that can be useful for many more users than just scientists and movie makers. Distributed computing has a potential future! Of course, I agree that people using their computer just for email and word processing won't need that, but they could much more easily help computation-demanding people, or even get paid for that*

Plus XGrid would have to graduate from Technology Preview.

----

The jump from Project Builder to XCode was big... how about a new Interface Builder?  Visually create and orginize not only classes and instances but protocols, categories, arbitrary methods (not just actions)... set up initial data by instantiating a NSMutableArray and populate it with NSMutableDictionaries...  on connecting a delegate get a list of delegate methods to choose to support (same for data sources and DragAndDrop)... register drag and drop types, notifications, etc.  Make XCode basically all about writing the blocks of your methods, and IB2 about setting up and orginizing your systems... (If we are going this far, make IB2 allow not just dragging on of headers, but .xcode files to update the whole system of relationships you have set up)

Or... we could keep Interface Builder for designing Interfaces and not end up with something more bloated than Mozilla. *Agreed 100%. This isn't RealBasic.*

IB is already about creating more than the user interface, but also for the interface between your code components and AppKit.  It doesn't need to be bloated, think adding a inspecter for classes that list protocols, (including informal ones) and you just check the ones you want to conform to (rather than trying to remember the exact signature of table view drag and drop methods etc.) For arrays/dictionaries, just make another IB palette, and use a property list editor view (like for info.plist...) in the inspector.  Populating initial data sets is already done in the inspector for NSComboBox...  Also one could add an inspector for views that lets you register drag types.  The reason RealBasic isn't good isn't that it makes customizing an application visually easy, it's that the language and libraries were poorly designed.  Cocoa and ObjC are finely enginereed, the point of InterfaceBuilder is to make it possible to rapidly prototype the interface, and build networks of object instances without having to write lots of lines of code.  Maybe the arbitrary functions thing is too far, but informal and formal protocol conformance could be a major benefit.  I don't hear grumbles about extra inspector panes for applescript, sherlock, or bindings... why not protocols, and drag types?  

----

The main things I want at this point are completely resolution independent display  and a filesystem supporting BFS-style file changed notifications.

Hmmm.... looks like 10.4 has the first of these experimentally, and spotlight certainly seems to use the second. Not sure if there's an API for it though.

YES YES YES!

Oh please, oh please, oh please!  Pretty please!  Gimme some fricken' file system mod notifcations.  Polling is bad, mkay.

Well, this didn't work for my app (a fast find file program without the downsides of locate) because it doesn't support huge numbers of files, but have a look at the kqueue API and try googling for UKKQueue (or possibly search cocoadev, it might be here). Not quite as cool as BFS, but not that bad.

----

Maybe there'll be a RegularExpression.framework in Tiger?

----

On the development side of things, what I want most is for Apple to officially support a high-level language with Cocoa. I'm not fussy which one they pick --- I'd prefer Ruby, but Perl or Python would be fine --- but if I have to stack method calls to NSString much longer I'll go mad. Give me a language with first-class strings, at least!

*IoLanguage!*

Wow... that is such a cool language. Thanks for linking to it.

*You're welcome. It's my favourite. -- RobRix*

----

How about Memory-Managed Objective C?  Why not?

*Why? We already have Cocoa-Java*

Because there isn't really any good reason to write Cocoa applications using Java. Mainly because there aren't any widely used platforms (with the exception of GNUStep) that implement Cocoa. Plus, JavaBlows. :)

I think the last thing we need right now is a change to the core Objective-C language, requiring us to change code and/or relearn some fundamentals.

Don't worry -- you wouldn't have to relearn anything for 2 reasons: think of the @try @catch @finally vs the old NS_DURING NS_HANDLER etc  both still work, just Apple brushed up their exception handling to look a little more civilized.  The other reason you need not worry is because there are two chances of auto GC being tacked on to Objective-C: no way and no how.  *I didn't know those @try blocks existed.  Do they actually work with NSException<nowiki/>s?* **Yes, they do. Take a look at *The Objective-C Programming Language" as of 10.3 or later.**

What I **really** want in Xcode is some breakpoint "groups" so I can turn certain groups of breakpoints on and off with a single checkpoint -- not a lot of code for sure to support that feature (I'd say all together <500 lines), but it would sure save me **a lot** of time when debuggering

What I'd like in XCode is some of the niftier stuff from Eclipse (*Don't you mean "niftier stuff from Visual Studio that was copied by Eclipse" ? ;-)*): Collapsing methods with disclosure triangles, refactoring, "quickfix", error finding while you type, working autocomplete, etc...

----

Does anyone know of any location that we (legal holders of the Tiger DP) can talk about new features, bugs, etc... without compromising our NDC agreement? I was thinking maybe of something that required you to log in using your ADC account?

----

Oooo!  Oooo!  I know what I want!  I want IB to have "folders" for the various class instances I have.  I don't know how many of you would actually benefit from this, but I often have LOTS of stuff in my nib -- the controller layer adds even more *stuff* -- and it would be nice to group all that clutter into some hierarchical organization.  Why?  So that it is easier to make, edit and maintain the nib, apart from simplifying the edit experience, the folders would in no way change the way the nib works or loads.  Now, no smart alecs please about how I should break big nibs up into several smaller ones; if you're working with the controller layer (as you probably should be) you often need a lot of instances in one nib.

----

Do you know you can switch between icon and outline view? I often find this useful when I have many objects in the same Nib.

http://www.top-house.dk/~aae0030/CocoaDev_MainMenuNib.jpg

Though I generally do not have that many objects in my Nibs -- I find that NSObjectController is rather redundant, and just bind everything to File's Owner, which is the controller responsible for the Nib.

--AllanOdgaard

No doubt about it -- outline view is a real space saver, but it only groups things together that are predefined to group (such as the menu items of a menu)... the outline view doesn't let me impose my own organizational hierarchy

*Personally I think IB could use a general overhaul. It's an excellent tool, don't get me wrong, but there are plenty of things about the interface that could stand a retooling. It annoys me that I can't drag-to-reorder things in the icon view, for instance. It also bugs the hell out of me that some things just *CANNOT* be deleted. Even after all connections are cleared and all content is deleted, I can't delete a panel *I created* from a nib. While this is certainly not the norm, it happens enough to be deemed a bug (yes I've reported it).*

I too have noticed the undeletable panel bug you talk off. I agree it is *extremely* annoying. How do you report bugs like this?

*bugreporter.apple.com - I have a report in right now. Reference problem id #3935575. I reused a panel that I couldn't delete just the other day, put some new controls on it, removed the auto save name, then decided I didn't want it anyway. Without thinking about it, I just deleted the panel and was surprised to see it was gone. I'm thinking either the adding new controls or removing the auto save name (the latter is my vote) is what did the trick. This has also been reported. PLEASE NOTE: I was unable to submit my nib due to NDA, so if ANY of you have a nib that's currently suffering this problem, send it to them referencing problem 3935575 if you can!!!*

*FYI, this could be due to the panel or one of its subviews being locked.*

I'll be happy if automator and interface builder will work together, even if through applescript studio. People have mentioned that we don't want IB to be as bloated as Mozilla, but this would be an awesome RAD "suite". Drag up a utility in automator, and then create an interface for it in IB.  *Weeeellll .... Automator is strictly a gui front-end to the scripting capabilities of other applications. Creating a GUI for an Automator script would make it an AppleScript app ...*

----

**It is a simple question that can be asked without breaking the NDA. Do not remove posts about a prerelease version of Tiger, unless your gonna remove this entire discussion, seeing as it's titled OSXTiger.** *information that Apple has made publically available can be discussed within the terms of your NDA. other information cannot.*

----

iPhoto 5 and iMovie HD are using a back-ported version of Tiger's CoreImage for adjustments, effects and transitions!
This is plain to see if you open up the application packages and poke about a bit.

The new "Adjust" panel in iPhoto, for example, allows you to make changes to exposure, colour balance etc and it works very smoothly, with changes visible in the image instantly. In contrast, using the adjustment controls in an application like Photoshop Elements, changes are much slower with noticeable calculation delays and visible redraws.

I thought this interesting because it means that a lot of the low level facilities that CoreImage relies upon must already be available in Panther...

----

Hey Apple! The Select ADC member at 64.77.160.218 needs his seed key suspended!

*I agree that he shouldn't ask, but I don't see that asking other people to break an NDA is grounds for having your seed key suspended, and he has not offered up any information in his own post.  But to the original poster:  It really is not possible to answer that question without breaking the NDA.  Read the terms again.*

----

My email was down for a couple days, and I was just wondering if anyone got the ADC email saying that the latest Tiger build has been mailed?

----

If you have questions about seeds, contact ADC. They can't be discussed here. What part of that is so difficult to understand?

*Um slick... that question was asked on February 2, 2005. The edit that was made today was, well, the following text: As of today, April 12, the official release date for Tiger is Friday, April 29!.*

