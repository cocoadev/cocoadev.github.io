---
layout: page
title: SendingAppleEventsToSelf
---

I have a plugin that loads into a larger app.  To some degree it interacts with the rest of the app through applescript - NSAppleScript calls from cocoa.

The problem is, on some installations it doesn't work.  The app claims not to understand commands addressed to it, for example "Can't get version" when asked for its version (yes, I know I can get the version from the app bundle).  I can't figure out any kind of correlation for what systems it does or does not work on.

Now, I'm *sure* I've read about this problem and how to solve it before.  I would have thought I read it here on cocoadev.  But I can't for the life of me find it!  Does this ring any bells for anyone?

----

When you ran executeAppleEvent:error, your second argument was a pointer to an NSDictionary, which should be populated with some sort of error description in the event that something goes wrong. What do you see in that dictionary after the execution fails?

It would be useful to know whether you're saying that the application won't respond at all to apple events on these affected computers, or if you're only having trouble communicating with the app from within your plugin. That is, can you fire up script editor and ask the app for its version number?

----

I can't test the error dictionary directly because this doesn't happen on any machine I have access to, only on a few users' machines.  But I'm throwing an exception that eventually makes it out to the console, so I can say that either the NSAppleScriptErrorMessage or the NSAppleScriptErrorBriefMessage is "iPhoto got an error: Can't get version."  (The plugin here is Keyword Assistant, http://homepage.mac.com/kenferry/software.html).

Now, on one user's machine, I asked him to try the script     tell application "iPhoto" to get version in Script Editor, and he got precisely "iPhoto got an error: Can't get version."  I told that user to reinstall iPhoto.  However, for another user iPhoto scripting works fine from script editor, but not when used from my plugin.  I sent him code that got the version from the app bundle instead and he promptly got an error on the next applescript call.  So it isn't just the version command.  This second user also reinstalled iPhoto for the heck of it, but it's no better.  There are two other users who have reported trouble but they haven't answered the email I sent them yet.  The vast majority of email I get is just thanking me, so I figure it works for most people.

My memory is that there is or was a specific issue where an app might not get, uh something having to do with apple events initialized properly if the first apple event sent to it comes from within the app.. and I thought there was a known fix.  But I might be making this all up.  Anyway, I'm interested in any ideas that anyone has.  The users having the problem aren't too techy, but they do seem to be willing to try stuff out if I ask.

By the way, the deal with the plugin is that I do as much as I can through applescript since there's a published interface.  But there are a few critical operations that can't be done from AppleScript (or the AppleScript support is buggy) so I have to use a plugin rather than a standalone app.

----

I'd suggest checking with the applescript-implementors list: http://www.lists.apple.com/mailman/listinfo/applescript-implementors/ . If the systemic problems you dimly recall really exist, that's the place to confirm it.

----

I finally reproduced this.  The call in question was happening in a method triggered by an NSApplicationDidFinishLaunching notification.  It turns out that on 10.2.8 (at least), that's too early to assume that an app has registered its handlers, and the app doesn't register handlers just because it is sent an apple event.

It looks like I can work around the problem by calling [NSScriptSuiteRegistry sharedScriptSuiteRegistry] before running my script.  This might have been what I was thinking of.

And now that I know what to search for, looks like at least one other person has come to the same conclusion. <http://www.cocoabuilder.com/archive/message/2002/9/13/60442>  No hits on applescript-implementors.

