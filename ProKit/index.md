---
layout: page
title: ProKit
---



ProKit is one of Mac OS X 10.3's PrivateFrameworks, containing a number of classes and categories that extend the FoundationKit and AppKit classes.

----

Interestingly, it appears to be a layer on top of the standard AppKit view classes, rather than a complete reimplementation of the AppKit. *isn't this kinda the point of OOP?*

*But what does it *do* and why is Apple using it but not making it available to us?*

----

Being too lazy to actually classdump it myself, what's in ProKit?

----

You don't need classdump if all you want is to see what's there, just 'nm path/to/ProKit'.

But it doesn't really matter what's in it, since it's a private framework and all.

----

For the insatiably curious, ProKit provides controls and UI for Apple's pro applications, including Final Cut Pro, DVD Studio Pro, Motion, Logic, and Wave Burner. Looking at the (obfusticated) image resources contained in the package reveals this. *It's also surprisingly easy to use these controls through InterfaceBuilder (thanks to OOP, as mentioned above), but of course, since it's a PrivateFramework, it's a bad idea to use it in anything that you ship out to others. Sure is fun to do, though!*

----

Does anyone know how you could subclass a class from the ProKit?

*Same way as you would any other class.*

----

**Reimplementing ProKit to make its functionality available to 3rd party developers**

I submitted a "feature request" via Apple's web site for the use of ProKit in 3rd party apps. I received a nice "we've passed your request on to the appropriate people" response, after the automated response. It's been a couple of weeks now, so I'm no longer expecting an answer. 

My app would complement the existing apps that use ProKit and shares the same general user base.
Apple was spot-on in creating ProKit for graphics/video pros working in an app all day.
Aqua blue, and even aqua graphite, are just too distracting from your own content.

Anyway, ProKit, as far as I can see, provides 1) a LookAndFeel, 2) a couple of new controls.

To get started, I am primarily concerned about the look, that is, neutral color backgrounds, smaller text etc.
The look doesn't necessarily have to match ProKit pixel-for-pixel -- however, bringing yet another LookAndFeel into the Mac UI space (aqua blue, aqua graphite, brushed metal, ProKit) is probably not going to win points with users.

After the basics are done some additional controls could be implemented too.

 
 * Does anyone have any idea as to how to go about reimplementing it?
 * Is subclassing most of the view classes the way to go?
 * Would you end up with a series of alternative IB palettes?
 * Is there a way to do it that is not "brittle" against future changes in Cocoa?

 * The key question is: How much work is involved?
 

It would be very easy to do the basics in Swing under JDK1.5. However, alternative Cocoa LAFs appear to be undocumented and perhaps even discouraged.

Perhaps this effort is not required if the application requires Tiger or later to run? Is this wishful thinking?

'----

*Is subclassing most of the view classes the way to go?*

I'd think so.  As long as the views *are* natural subclasses of typical NS views.

*Would you end up with a series of alternative IB palettes?*

If you want.. but you can always set an IB object to be of a custom subclass of an NS object.  

*Is there a way to do it that is not "brittle" against future changes in Cocoa?*

It wouldn't be brittle at all.  If you build using documented API, you should be okay.

*How much work is involved?*

I can't answer that because I don't know what's in prokit.  What is it you want?  Are there control-types that aren't in public cocoa, or do you just want the look?

----

The big question: Wouldn't this violate Apple's copyright on the look of ProKit? From what I've seen, creating a workalike is generally fine (unless a software patent is involved), but creating a lookalike is wading into some pretty dangerous waters.

----

I'm pretty sure Apple wants to reserve ProKit as a distinctive L&F for their Pro apps, and they won't want you using it.

