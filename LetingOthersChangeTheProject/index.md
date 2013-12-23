---
layout: page
title: LetingOthersChangeTheProject
---

  

I have written a plugin project using the article on cocoadevcentral. I want other people to create plugins easily by changing the code i wrote. To make it simple i prefer other programmers don't have to create a new project and change project 's settings and variables, so the idea is to copy the project and only change the code. I've heard about saving projects as templates. When the template is loaded, a copy of the project is created, so the developer can work on this copy. Do you know anything about these templates? How do i save a project as a template?
Thanks in advance
     Angel Puerto

----

You can create a new template for Xcode by putting the project in the "/Library/Application Support/Apple/Developer Tools/Project Templates" directory. Place it in the appropriate folder and it will appear in the list when creating a new project in Xcode. You can even add your own folders to create categories in the list. Look at the other templates in that directory for examples. As a side note, you can also create file templates by adding them to the "File Templates" folder. I find these to be extremely useful. -- RyanBates

The way I see it, there ought to be an optional directory in app bundles: MyApp.app/Contents/Xcode Templates/.  Xcode then scans the applications on the machine for templates to show on the list, making it simple to find a template for a plug-in to an app you have installed.  -- PeterJaros

*Yeah, but how often does a random Cocoa application -- no, **any** application bundle -- use a plugin that can be designed in Xcode?*

Hmm?  Can you name a cocoa app that supports plugins but for which xcode isn't sufficient to design the plugin?  Peter � good idea!

*The point was that the app bundles have to be written in one of the few Xcode-supported languages and have to also use plugins. Most applications do not meet both of these criteria. It would be a moderately large (and avoidable) drain on resources for Xcode to search every application bundle on the system (maybe even the entire network) for a few plugin templates.*

----

(this comment was lost after concurrent updates - another reason why Wikis really aren't the way to do this kind of thing)

Very, very few programs are the kind which require templates and for those, why would every user need all of that extra stuff installed when less than 1% of the user base is interested in developing plugins. Simply distribute the template with some documentation as an SDK on the image. That way developers who are interested will know that it is there and the average user can just ignore it.

The real down-side, of course, would be the overhead in examining every installed bundle for XCode Templates. Plus, it would be annoying to developers who aren't interested in developing plugins for your app, or even at all, since every launch of XCode would take a long time and thrash their disk only to present them with all these project templates that they now have to scroll through to get to the few that they are actually interested in (when creating a new project). This problem grows as you start to consider other resources which may be of use (ie: IB Palettes) and need to be installed with the SDK but aren't part of the template.

Moreover, templates are more general purpose than just for creating plugins so we would still need the Application Support location. In my opinion, this wouldn't solve any problem or make the current approach to templates and plugin development more elegant. It would, however, be a nightmare for XCode, an annoyance to casual users, and an occasional frustration to developers.

----

It wouldn't necessarily have to search the system, you can imagine a setup where the user could elect to develop a plugin and be prompted for the app to develop for.  Maybe XCode could accept drags of applications.  Anyway, I repeat, name one example.  Xcode can deal with ObjC, Java, AppleScript, python, Perl (maybe Ruby?), and that's all the languages that I can think of that have a cocoa bridge.  Realistically, cocoa programs are always developable in xcode.  And even if not true in the absolute, certainly most are, and it isn't like this *has* to hold for all apps to be useful.

The alternative, which we have now, is that every app that wants to give you a template set has to insert new entries in the global templates list.  This pollutes the list.  Did I really need "Address Book Plug-in for C", "Address Book Plug-in for Objective-C" and "AppleScript XCode plugin" greeting me every time I want to create a new project?  

**Xcode doesn't know whether an application bundle uses Cocoa or not. Theoretically, *any* bundle with a type code of APPL will be considered an application.**

Yes, and most applications won't have plug in templates anyway.  That's not a big deal.  As with dragging applications onto Script Editor, xcode could just say that there are no templates available.  I suppose the Carbon/Cocoa distinction is not as important as the bundle/flat file distinction.  No reason why Carbon apps couldn't have XCode templates.

----

*The usual advice: send this to Apple via http://bugreport.apple.com
*

----

True.  One reason it's worth discussing though is that we could possibly do it ourselves with xcode's scripting support.  No drag and drop onto XCode icon, but that's not a central point.

