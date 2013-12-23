---
layout: page
title: NSMenuItemIssue
---


I would like to be sure that my understanding from the following issue is correct.

My goal is to "intercept" the selection of a NSMenuItem inside a NSMenu.

Is it correct that since there is no way to use menuRepresentation and reach NSMenuView, cocoa is not able to let the programmer to reach that event?

In this case, how Interface Builder does? (when editing submenus)

Thanks for help,
Michel Vaillant

----

Hi, Michel,

You have not made clear your reasons for "intercepting" a menu item or even what you mean by the word. All I can think of is NSMenuValidation, but you probably already know about that. Read the question-and-answer at the end of the NSMenu page. It looks spookily like where your own confusion is leading.

----

I will try to explain it better:
I need to have a callback called, each time that a Menu Item is highlighted; at that moment  I need to show a feedback to the user.
I saw NSMenuValidation, and every thing written about cocoa's menu. Tried to connect NSMenuItemCell, but without success.

----

NSMenuItemCell is used in popup lists, not in the menu bar.

It*s obvious that you want to show additional feedback simply upon the selection of a menu item. You say you "must" do this, but I think what you mean is that you "desire" to do this. There are always alternatives for things you "want" to do. If you cannot or will not describe your motivation, it will be difficult for anyone to suggest an alternative. Otherwise you may need to dig into the Carbon underpinnings of the menu. 

It appears that you have not digested the information available on the pages linked from here, or from the documentation. The fact that the menu item is highlighted is feedback enough for me and my users. If you need to do something different, try re-thinking your interface, and some alternative means of giving feedback to your users may present itself.

I say again, look at the conversation at the end of the NSMenu page. It is exactly like the pointless exchange we are having now. OK, screw that: I'll quote it for you here:

**
I have been trying how to figure out if a user clicks on a menu item in the menu bar. From what I have found, it seems that I'll have to get it via Carbon. I have been googling for a couple of hours, and haven't found much. I think that MenuChoice? is the way to go, but I have no idea how to access this from Cocoa. I -very- little Carbon experience, and dont know the first thing about using code like that. If anyone can give me a shove in the right direction, it would be greatly appreciated. Thanks,

-- Jacob

*What are you trying to do?*

I'm trying to figure out when someone is clicking (and holding) on a main menu item (ie. when someone clicks on the "File" item).

*Yes, I understood that much. My question is, what is your end goal? What are you trying to accomplish? I ask this because there may be a better or simpler way of doing it, but we can't tell you what that way might be if we don't know what "it" is.*
**

So you see, Michel, someone has asked essentially the same question you have, that someone has provided no detail, and has been not been answered because no one could figure out what he really wanted. We're struggling here with a language barrier too, Michel. It is obvious you are not fluent in English. This is not to criticize you, but it *is* something we are stuck with for the time being.

----

Michel's English seems perfectly clear to me. He's looking for a way to get a notification when the user highlights a menu item. Exactly *why* he wants to do that is immaterial unless it happens that you don't know how to do what he wants but still feel the need to insert yourself into the conversation. To help with that, let's just make up a few possible reasons one might want to know when the user highlights a menu item. Perhaps Michel is writing software to help the visually impaired use MacOS X, and he's looking to fix VoiceOver so that it speaks the names of menu items as well as menu titles. Or perhaps he's working on a PhD in psychology or human-computer interaction or some such thing, and he's trying to measure users' ability to differentiate between similar menu items quickly. Maybe he's trying to record recent user actions for some reason. Use your imagination and I'm sure you can come up with more.

Michel, one thing that's not clear is whether you're trying to intercept menu item highlighting in your own application or in some application(s) for which you don't have source code. If it's your own code, you may be able to register for the Carbon event **kEventMenuTargetItem** to get a notification when the mouse passes over a menu item. On the other hand, if you're dealing with third party applications, I'd guess you'll need to get sneaky and do some code injection to get the notifications you want. -CS

----

Actually, knowing *why* is critical to helping people in situations like this. Why, you ask? Because people who need help, by their very nature, don't know the best approach for doing what they want to do. It frequently happens that a person wishes to perform some action A. Through a chain of reasoning, he arrives at a possible approach, B. But in order to accomplish B, he must do C. He doesn't know how to do C, and so he asks for help with C. In fact C is impossible/intractable/discouraged. Also, C is so far from A that none of the people helping him can see any connection to A, and therefore these knowledgeable people cannot recommend the much simpler and more straightforward answer of using D to accomplish A.

It's also helpful to know why to do something just because humans are often better at concrete thought than purely abstract thought.

As an example, take a look at this thread:

http://www.cocoabuilder.com/archive/message/cocoa/2004/2/10/96840

C, which is the question first visible on the list, was, "Why can't I copy an NSEnumerator, and how can I fake it?" This is due to B, which was, "Use an enumerator, and copy it in the middle of the loop." All of this came about because of A, which was *not* mentioned in the original question. A was, "How can I use enumerators to check for pairs of objects in an array?" Once A was actually stated in the second message by the original poster to this thread, the straightforward answer D was arrived at, "Use reverseObjectEnumerator and a comparison to terminate enumeration early."

If the original poster had never stated A, either because he felt it was unnecessary or because other list denizens jumped up saying, "Why is immaterial!" then he never would have gotten the solution he needed and the people helping him would have felt frustrated rather than useful.

It's entirely possible that A is complex and D does not exist here. However, it's also possible that A is easy and D is obvious for people more versed in the frameworks. We won't know until and unless the original poster tells us what A is. Discouraging him from saying so, and discouraging us from asking, is less than smart. -- PrimeOperator

----
Thank you for your help.
First excuse me for my "light" English, I didn't want to create a philosophical issue with my "menu" question. The reason I need to do that, is that my customer specified it clearly to me, and I will not be payed if I do not do that! So we can not do this with cocoa and I should use carbon with **kEventMenuTargetItem**. 
Thanks again for your time, 
Michel

PS:What stand strange for me is that Interface builder while editing menus has this behaviour. Is it coded in carbon?

----

I don't think you have to abandon Cocoa entirely just to install a Carbon event handler. Also, Interface Builder is an unusual program, and it's probably best not to assume that it does what it does using completely standard Cocoa classes. I think there's a lot of magic and a lot of legacy stuff in there. -CS

----

I don't think there is a lot of magic in Interface Builder.  I think that when you edit a menu in Interface Builder, you are not editing a menu.  You are editing a place holder for a menu.  The object you are editing includes a window that contains graphical representations for the top level menu and nested menu items.  When the MainMenu.nib file is loaded in your application, the place holders that you edited in IB are unarchived, and the structure of the place holders is used to construct an entirely different yucky Carbon menu.  There may have been a time (in NeXTstep before Openstep Enterprise for Windows) when IB let you edit the actual object that would end up populating the main menu.  If so, that was probably abandoned when Openstep Enterprise for Windows let you use IB to edit Windows menu layout.  [The vast reduction of the Object oriented-ness of "Cocoa" menus in the transition from Openstep to Mac OS X is a sad loss.  There really are no "Cocoa" menus in Mac OS X.  Menus are all implemented using "yucky" Carbon and break many assumptions that a programmer could make when he knew he had full fledged object in his menu.  For example, we can no longer subclass NSMenuItemCell and use it in the main menu.  We can no longer pop-up or hide the main menu via Cocoa...we can no longer tear off submenus...we can no longer experiment with pie shaped menus using Cocoa...

----
Thanks for these precisions.
Ah no, I am working on a huge program, everything is done in cocoa now (I leaned it just last year). The first real issue I had, that I didn't be able to realise was this Menu Highlighting event handling. I will do juste menus in carbon. I think that cocoa is a great "environment" (I am use to XWindow and Windows environments), I enjoy in particular "Categories" "Delegates" .... I suppose that Apple will take care of developers "desires" and will open a little more Menus to us.
Michel

