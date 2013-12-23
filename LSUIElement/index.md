---
layout: page
title: LSUIElement
---

See: http://developer.apple.com/documentation/MacOSX/Conceptual/BPRuntimeConfig/Articles/PListKeys.html

Specifies that an application is background-only *but* has a UI as well (usually a floating utility window). No menu bar or Dock icon, with the exception that it may have an NSStatusItem.

See also LSBackgroundOnly.
----

Q. Cool - where do I specify it in my app

A. Project Menu -> Edit Active Build -> Add LSUIElement to the expert view. 

Q. the above is only for Project Builder - no such menu option is available in XCode..  :-(  Anyone able to help out on this?

A. add it in the plist manually 

A. "Project" -> "Edit Active Target '...'".  In "Info.plist Entries" click on "Expert View", click "New Sibling" button, set name to "LSUIElement" value to "1"

----

Can anyone confirm that this is broken in Panther? I have my app with a setting of 0 and it does not respond to it. (After a restart of the app).

*It should be set to 1, but Panther does more aggressive caching of Info.plist, so you probably need to reboot before your changes take effect.*

Yeah sorry...I have been using it a fair bit on Jaguar and I posted a bit late. I will check the reboot...silly Panther, although Echidna works fine. :-/

There's an API to change the app's element mode on the fly. I don't know what it is offhand, though.

Does anyone know the above API?

I doubt that it exists -- LS-keys are for the LaunchServices, and the dock icon is added to the dock when the program is launched -- being able to toggle this state while the program is running makes no sense. But I might be wrong ;)

From the Panther release notes:
A new API, TransformProcessType, may be used to convert a UIElement or BackgroundOnly application into a regular foregroundable application with a menubar and Dock icon (2848736).

----

As per this email:

http://www.cocoabuilder.com/archive/message/cocoa/2003/11/1/85001

You need to touch the application bundle so that the info.plist changes are noticed by the OS. As noted by the poster, the caching of this information is a bad thing. Hopefully Apple does a really quick check of applications before it launches them in a 10.3.x update.

*At one point there was a check, if I'm not mistaken. Apple removed it for the 10.2 update in order to speed up application launch times.* http://itshumour.blogspot.com/2009/09/top-10-hilarious-quotes.html

<code>

  [[NSFileManager defaultManager] changeFileAttributes:[NSDictionary 
dictionaryWithObject:[NSDate date] forKey:NSFileModificationDate] atPath:[[NSBundle mainBundle] bundlePath]];

</code>

----

I looked around that mailing list some more and came accross this email:

http://www.cocoabuilder.com/archive/message/cocoa/2003/11/5/1374

Could someone interpret for me what this developer is suggesting?  I don't quite grasp the concept.  Apparently, there is 1 bundle (the main application) and two separate "launcher" programs that load this bundle (that's how I'm understanding things).  These "launcher" programs have different info.plist's (the only differ in the LSUIElement value).  These "launcher's" are "shell" programs.  What does that mean?

And finally, how does the program determine which "launcher/shell" program to "launch" when the user double clicks?

Thanks.

----

You say this:

"Apparently, there is 1 bundle (the main application) and two separate "launcher" programs that load this bundle (that's how I'm understanding things).  These "launcher" programs have different info.plist's (the only differ in the LSUIElement value)."

That's correct -- and that's all there is. He's just being ambiguous when he calls them "shell" programs; they're actually regular Cocoa or Carbon applications with their own application packages, and so would be different icons in the Finder. He's probably storing the bundle somewhere global, like /Library/ApplicationSupport.

----

Does this suggest that the two application packages (e.g. myDock.app and myMenuBarOnly.app) are complety identical except for the bit for their LSUIElement?  More specifically, do they contain duplicate code?  Do they provide any functionality besides drawing the menu for the dock or the menu bar?  And do they store their own data, or do they somehow communicated with the "launcher" program to determine what must be displayed in the menu bar.

Thanks, Joe

----

I'm the developer who made the suggestion and uses this method - sorry for being ambiguous with the "shell" terminology.  Indeed, the two small programs are launchers - they contain no application-specific code, just the calls necessary to load the bundle containing the real functionality, resources, etc.  The launchers are identical, as far as the source code goes - the only difference in them is the LSUIElement value in the plist.

 - Jon

----

I can't open the previous URLs. Can anyody explain me what are these applications?

Thanks

----
I've updated the links above.  -Joe

Thank you very much!

----

How is it possible to change the LSUIElement key value programmatically? (say you want the user to specify whether there is an icon or not in a preference panel) 

Thanks,

T

----

The applications who do break one of the Most Sacred Rules of OS X programming by modifying data inside the application bundle -- specifically by rewriting the Info.plist file, I believe. That of course may or may not work, depending on the owner of the .plist and the current access privileges of the app's user. --EmanueleVulcano aka l0ne

----

That's Okay. Rules are meant to be broken! Yet it doesn't work... I rewrote the info.plist. I can see the LSUIElement value changed. But it seems that the system cached the file and doesn't update my changes although I restart the application. So then I tried things like that after the changes:
		
  system([[NSString stringWithFormat:@"/System/Library/Frameworks/ApplicationServices.framework/Frameworks/LaunchServices.framework/Support/lsregister -r -f '%@' -v", appPath] cStringUsingEncoding: NSUTF8StringEncoding]);

but no success... How can I register that Info.plist when it's been changed?

Thanks

T

*This would be one reason why you're not supposed to be breaking this rule.*

----

The OS caches the values inside of the appliation bundle.  You must change the modification date of the program.  One way is to use the command line program "touch." e.g. 		[NSTask launchedTaskWithLaunchPath:@"/usr/bin/touch" arguments:[NSArray arrayWithObject:[[NSBundle mainBundle] bundlePath]]];

----

Without knowing specifics, one thing you could do is make your background program always hidden, and create a second lightweight configuration application which contains the dock icon and can either be running or not. It might be a little more work, but overall I think it's a better idea than trying to modify the plist, especially since you won't have to restart the app to change that setting.

----

What is the alternate way if I'm using .bundle that read & modify other .app's plist?

E.g. [NSTask launchedTaskWithLaunchPath:@"/usr/bin/touch" arguments:[NSArray arrayWithObject:[[NSBundle mainBundle] bundlePath]]];

----

I've tried to exhaustively document LSUIElement use and other means of accomplishing dockless mode in this blog post: 

http://codesorcery.net/2008/02/06/feature-requests-versus-the-right-way-to-do-it

----

I thought I'd throw in my two cents here. I've been investigating this for my new application launcher, and I've come up with a very clean, working solution using TransformProcessType() on 10.5. I just haven't been able to test it yet on 10.4 to see if any system bugs crop up. (Added credit where due... When recently looking at the Quicksilver code posted at code.google.com, I noticed that this is the exact method that QS uses.)

In my Info.plist, I simply set LSUIElement to TRUE and then in my app's delegate applicationWillFinishLaunching: method, I check a user pref from standardUserDefaults and call TransformProcessType() if needed. This is early enough that the dock icon shows the "running" dot properly and the app seems to activate, deactivate and behave as expected.

