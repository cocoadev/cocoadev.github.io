---
layout: page
title: DisableAlertBoxInNSSavePanel
---



I use NSSavePanel to ask user to enter the file name to save, when the file name that user entered has already existed in file system, NSSavePanel will display an alert box asking user will replace the existing file or choose another file name. And I'm willing to disable the alert box, so that NSSavePanel will always replace the existing file without an alert box.

I tried a few times, but all failed. Anyone here can give me a hint? Thank you all guys in advance.

----

In one sentence: Don't do this. 

For one thing, your user might **expect** to be warned and will most definitely not appreciate the standard OS-wide behavior to not apply in a special case. For another thing, this functionality of the save panel is private **because** you, the developer, don't know everything (like your user's intention at that moment) and shouldn't be allowed to whack files without warning.

I can't stress this enough: *This is a bad, bad, BAD ... *BAD* thing to do.** I recommend you find another approach (as in, re-think your UI).

----
I disagree entirely.

I wrote an application that saves log files to the disk. It prompts for a file using NSSavePanel. If the file already exists, the app does *not* overwrite, but merely appends. I would want to either remove the warning entirely, or replace it with my own, because the built-in warning is completely false. But as far as I know, this is not an option. It's been suggested that I can "simply" use two commands, one which brings up an NSSavePanel for creating new files, and one which brings up an NSOpenPanel for appending to existing files, but this is kludgey at best.

----

Then why not ask once for the file, then simply write to that file every time afterward?? I still don't  understand why this is necessary or even preferable.

----
I don't really understand your suggestion. The user can open a window, which displays events in realtime. The user can log these events to a file by clicking a button, then choosing the log file. With your suggestion, the user would choose once, then on subsequent invocations of the program, it would default to the same file? What if he wants to change to a different file? What if he wants to change to a file that already exists, so he can append the new logs to that file?

----

That's why you make it an option ... a preference ... a choice. Why would you prompt the user every time for a save-to location but allow them to simply whack the file contents? This is **inconsistent with Mac OS X behavior**. Period. I would personally not trust an application that simply whacks a file without making sure I want to overwrite it first. This is why I suggested you rethink your UI approach - if the log file path is a preference, the user knows that it will always use this file. You should only prompt for a new file path if the user wants to change it - then append to the same file (or overwrite it, as long as there's a warning somewhere that this is what will happen). Anything else is completely at odds with the expected behavior.

Of course you can do whatever you like; you're the developer. Just know that your approach is non-standard (and will not inspire trust in users when files are simply whacked without the user being asked if that's okay every time they're prompted for a save-to path). I've said my piece; do whatever you like.

----
I never said I want to remove the warning. I only want to *customize* it. Being able to remove it would allow me to do this, as I could simply disable it, then show my own. Or if it simply allowed me to provide some custom text, that would be fine too. I don't want to not show a warning, I simply want to show a warning which is actually *true*; the file will not be overwritten, it will merely be appended to.

Making it a preference is not something I want to do. It's possible to have many windows open, and the user will probably not want them to all log to the same file.

I don't see how my approach is non-standard. I'm not aware of any standard applications that append rather than overwriting in this way, so customizing the warning is at least as standard an approach as any. But you keep up your ranting if it makes you feel better.

----

Another voice, here: Correct me if I'm wrong about your intentions, but you object to the standard warning because it implies that an existing file will be over-written, rather than appended to, and you think that the standard alert would be misleading in that context. This is reasonable as far as it goes, but it does not really solve the problem of introducing non-standard behavior in a standard UI device. I can see why you might feel like it would be neat to be able to do it, but no way can I see this kind of behavior as essential to your purpose. Whatever that might be - you have been way less-than-forthcoming in that regard. Maybe what you should do is change the behavior of your windows so that you wouldn't feel the need to re-set the names of the files into which you log their events.

And as far as the appropriateness to customize the warning **because** your app's behavior is to append rather than overwrite: That is the most non-standard aspect of your description. If your needs call for something that different, why do you want to use the standard save panel at all? 
You **need** a file system browser? Feh! How do YOU know the user will "probably" not want all the logging to go to the same file: If your logging code contains sufficient information, why would that be unsatisfactory? Unless that one file is just going to grow too large, for some reason. And it would, if you just kept appending and appending and appending....

----
Ok, you want forthcoming: the application is StreamWatcher ( http://www.eloquentsw.com/streamwatcher.html ). It's basically a network sniffer that does a great deal of automatic summarizing. The logs it creates will show stream start/stop events, along with information such as how much data was transmitted, etc.

Currently, enabling logging is done per session. When you have an open window, you can click a toolbar button to start logging. The user is allowed to choose a file using a standard save sheet, which lies as to the replace behavior, since it appends to an existing file instead of replacing. I don't want to make the log file be a preference because the user may want to log different interfaces to different files.

More generally speaking, if an application has a situation which calls for selecting a new file, or an existing file, but the behavior is *not* to overwrite the existing file (for whatever reason), is this simply something that should not be done?

----

Well, I see from your screenshot that it appears to be a document based application (but may not be!), and you title your *window* with the port and IP (I guess on the local network), and well, yes, you may be on that one again someday, appending to the same file. I take that you want to append because you want a cumulative history of the performance of that particular port/IP combination, or something to that effect. And you want to give your users some control over WHERE they save the files. Perhaps you should set up a serial file naming scheme for any particular IP, but you might end up with file proliferation in that case. At some point, your users are going to have to manage their own databases. If you just use C library file calls with append mode operations, of course, the users won't be choosing where the files go (unless it is through a Prefs window - you could always FORCE them to log different interfaces to different files), but at least the files won't be proliferating. As Steven Wright says, "You can't have everything. Where would you put it all?"

----
You said, "I never said I want to remove the warning. I only want to *customize* it." Yes, exactly -- DON'T MESS WITH MY STANDARD UI.

----

And furthermore, the title (and initial post) read "disable" the alert box, and the initial post reads "replace the existing file *without* an alert box". It should be noted that the OP and the person who responded ":I disagree entirely" may not be one and the same person. The author of Stream Watcher clearly has enough on the ball to find another solution to his problem. The OP (likely a different individual) is just hacking for the sake of it. An app that does what the OP wants is not one that most of us would want to use.

----

If you're using the document-based app template, I can recommend something to make it easy on yourself. Just make the standard behavior to save to a new file. Then, add another method and a menu entry for "Append to existing file" or something like that. Just copy the text of the existing file, add the new stuff, and it's done. After you've created that method, you can have the default save method check the file it's saving to, and if it already exists, you could present an alert box, and ask for one of two choices: "Append" or "Overwrite"? Then, from there you could add a little checkbox if you wanted to that would allow them to always do the chosen action, without asking again, and the obvious preference to change that. This is the way that it's been done before, and it follows the HIG as best I can tell. And it's pretty easy to do. Laziness is always the way to go ;)

I'm experiencing a problem related to this alert box too. Is it just possible to know what has been clicked in this panel? Some kind of notification? - StephaneDassieu

----

You can achieve the behavior that you want by overriding the following method in NSSavePanel.

- (BOOL)_overwriteExistingFileCheck:(NSString *)filename

This is a private method and as such _CAN_ break. - VinayVenkatesh

