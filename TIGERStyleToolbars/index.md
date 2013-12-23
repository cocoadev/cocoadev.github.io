---
layout: page
title: TIGERStyleToolbars
---


I am wondering if anyone has any insight on creating Tiger style toolbars as seen in Mail 2.0. I have looked into subclassing General/NSToolbar to no avail. 

-Julian

----

Step 1: Purchase Tiger. Step 2: Create an app with a toolbar. Step 3: Run.

----

1. You cannot purchase Tiger, but I am a developer so I get the seed. 2. You must have never seen the toolbar in Mail.app 2.0. It is a gradient and draggle like a Textured toolbar. So, anyone???

----

6. Confidential Information. You agree that all information disclosed by Apple to you that
relates to Apple�s products, designs, business plans, business opportunities, finances, research,
development, know-how, personnel, or third-party confidential information, will be considered and
referred to collectively as �Confidential Information.� Confidential Information, however, does not
include: (a) information that Apple makes generally available to the public; (b) information that you
can demonstrate to have had rightfully in your possession prior to disclosure to you by Apple; (c)
information that is independently developed by you without the use of any Confidential Information;
(d) information that you rightfully obtain from a third party who has been given the right to transfer or
disclose it by Apple; or (e) any third party software and/or documentation provided by Apple and
accompanied by licensing terms that do not impose confidentiality obligations on the use or disclosure
of such software and/or documentation, for example, software governed by the GNU General Public
License (�GPL�). You agree not to disclose, publish, or disseminate Confidential Information to
anyone other than those employees and contractors working for the same entity as you who have an
existing ADC membership. You further agree to take reasonable precautions to prevent any
unauthorized use, disclosure, publication, or dissemination of Confidential Information. You agree
not to use Confidential Information otherwise for your own or any third party�s benefit without the
prior written approval of an authorized representative of Apple in each instance.

----

Which basically means that anybody with the seed CANNOT talk about Tiger, including you, before Tiger's officially out. Come back April 15th or whenever Tiger hits the shelves and ask us again. *This sentence was removed because it **is** a NDA violation.*

----

Actually, the look of the toolbar hardly falls under "confidential information". You can see it in its full fuglyness at http://www.apple.com/macosx/tiger/mail.html . Why you would want to copy that look, though, is totally beyond me. I just hope there will be a way to turn it off.

*The look doesn't, but the implementation certainly does.*

----
The icons are ugly, but the gradient is awesome all around.

* Agreed. The rounded toolbar buttons in Mail.app cause contrast problems, especially with the "junk mail" icon. But I do like the gradient toolbar look. *

----

In the text I removed, you just posted something Tiger only from a header/documentation. Please do not do this. You'll have your butt in a sling if Apple comes after you, and General/StevenFrank could get in some hot water. So refrain from posting Tiger details against your NDA.

----

Okay, I will never do that again. To the original poster: if it's a public feature, as it likely is, look in the documentation and you'll probably find it. If it isn't, then, poor luck.

----

Does anyone else find this toolbar hideous?

----

What the heck is going on here??  The original poster is asking about getting the look of the toolbar by subclassing General/NSToolbar.  There is NO WAY that that is an NDA violation.  Can we ease off on this NDA crap a bit?

*I whole-heartedly agree. Apple themselves made the look and feel public months ago. Speculating on how to achieve it (which I assume must be for pre-Tiger apps) is none of Apple's damned business and far beyond the reach of an NDA.*

However, the issue at hand was that someone posted a constant name that can only be found in header files or the documention that is part of a Tiger install. Therefore, it is a breach of NDA, and should not be posted here.

----

I am the original poster, and yes I am looking for a way to achieve this look by subclassing or other means for both PANTHER and TIGER. Anyway I figured out how it is done: It's not the General/NSToolbar, it's the window itself. Subclass General/NSWindow, when you init it set it to textured and call setBottomCornerRounded:NO] then in - (void)drawRect:(General/NSRect)rect {} just draw a gradient from top to bottom using General/NSBezierpath. Thats all folks!

-Julian

----

But the Tiger one has a nifty gradient transform when its hidden ;).

----

Everyone file bug reports about Mail's ugly toolbar items. Oh lord, they're hideous!

----

Hehe.  It might be a good idea to couch the complaint in objective language of sort.  For example, "inconsistent with standard Mac OS X toolbars without any added benefits" rather than "AUGH, IT BURNS!!!"  Even better would be if we could figure out why they *did* change the toolbar style (there oughta be some reason, right?) and see if we can't come up with an approach that accomplishes the same goal without looking like ass.

----

*It's not the General/NSToolbar, it's the window itself.* - Damn! Do you mean that Mail no longer has a customizable toolbar?! That's a shame, dammit. Oh, and by the way, you can mimic this ugly design in a real toolbar by installing your own views and controls into it. At the very least users will be able to hide those godawful buttons.

----

I notice that all of Apple's programs are moving more towards this style, but each with a different look. For example, Safari has similar buttons, but square and iTunes' are round. Is this the way of the future? I hope not! I like the traditional toolbar look. There is something very pleasent about the regular toolbar icons that is missing in these lousy buttons. However, I don't think the Mail buttons are inconsistent. If you look, they do line up to the standard square button. It is just that they are ulgy. What is inconsistent is the gradient replacing the pinstripe background and not in the brushed metal kind of way. -- However, the gradient is only an option for windows with a toolbar, you can IB in and remove that if you want.

*Can you describe this process, or give a pointer that describes it?* **Not yet, Tiger isn't out yet! Wait until the 29th. :)**

----

They way to get this toolbar is pretty simple.  Interface Builder now has an option called "Unified title/toolbar look" associated with the attributes of General/NSWindow.  Have that enabled and then go through the usual steps of adding a toolbar to your General/NSWindow.  You could also accomplish the same through instantiating the General/NSWindow yourself and passing it the constant General/NSUnifiedTitleAndToolbarWindowMask.

Hope that helps!

*But please don't, its hideous, infact I wrote a hack to remove this all together!*

Um... which is hideous, the icons or the title/toolbar gradient?

The gradient (not taking into account mail's fugly icons).

Well, um... the gradient looks perfect, as well as making it clear that the toolbar belongs to the window.
