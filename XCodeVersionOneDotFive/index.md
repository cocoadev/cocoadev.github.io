---
layout: page
title: XCodeVersionOneDotFive
---



[Topic]

Well it's out in the open.  What do you guys think?  SVN support is pretty nice, some of the basic UI improvements are nice... what are your experiences?

----

Smooth ride here, once I was actually able to connect to the server yesterday. The editor is much faster - I may actually switch back to it from General/SubEthaEdit. But it looks like they took the toolbar out of the editor window, so you can't just build from there. You also can't completely hide the files list on the right anymore. Clicking on build warnings/errors in the file list no longer opens the embedded editor sometimes. Code completion seems more annoying - I haven't quite figured out how though yet... maybe just 'cause it's faster to popup now.

----
Know what you mean about code completion, though the toolbar can be reverted back to what you had.  You can also set the delay on code completion... though you're right, it's more than just the delay... AND the files DO hide if you want.

----

*the toolbar can be reverted back to what you had*

The editor window toolbar? How? It's just completely gone for me - no tic-tac in the titlebar, View -> Show Toolbar is disabled, and Customize Toolbar... does nothing.

*Hmm... weird. I just chose 'Open In Separate Editor' from the file list context menu and got an editor window with a toolbar... 'Open As...' gives one with no toolbar.*

----

It says it integrated subversion and I was hoping it finally would have a SVCS built-in with like no work to configure, (unlike previous versions), but it still doesn't seem to be set up like I wanted... oh well... One thing that does bug me though is the way I used to set up my project window doesn't work anymore... I used to have the project window show just the tree of files/smart groups/targets etc, without a second pane showing all the files, and then each thing in a seperate editor.  This way I could  have a little tiny window with access to sources/resources/build settings etc. plus one for each file I was editing.  I can nearly accomplish the same effect, but I may change my habits...Oh, and they moved the documentation breaking General/QuickSilver-s links to it.

----

I too was having trouble trying to download the segments, but I saw that today (8/6), they posted the full disk image download so I downloaded that (used IE... seemed to have better FTP support than Safari). Xcode 1.5 is much faster! The editor is really a lot smoother. Everything's been improved.

----

I did not have as much luck, even though I can start to download, the speed tops 4K/s... this will take a while. Why can't Apple but it out on General/BitTorrent? That would be better for all. --General/TheoHultberg/Iconara

*Except for me, since I can't **start** torrents from home, but can only continue them. I got the download at around 230K/sec in a small city in central Ontario. I'm **very** pleased with things right now.* -- General/RobRix (not meaning to brag.)

----

Here is an annoying thing in code completion. Say I type in setAllowsCustomization:TRUE, the code completion menu will stay open with TRUE as the only option and surprisingly it will block all key events that have a modifier, like Cmd-S and Cmd-R. Quite annoying, I have run into it several times tonight. -- General/MatPeterson

----

I tried code completion once, but it was too annoying for me. I just gave up on it, and memorized everything ;-) --General/KevinWojniak

----

With 1.5 if I want the editor to be in the same window as my file tree it has that annoying divider taking up space.  And if you try dragging the divider further up it seems to create some sort of line numbering error.  Guess its time to use SEE as my external editor.

----

I have no complaints, everything seems just a little more polished. I'm a little troubled by the lack of the the compiler settings categories drawer in the build settings window. But, one big thing that bothers me is that the new documentation breaks General/DocoaBrowser. 

I hate Xcode's built-in documentation browser. So, I guess I need to download Docoa's source and see if I can't figure out what the problem is.

--General/ShamylZakariya

* I found the issue in Docoa's sources and have sent a patch to the maintainer. --SZ *

 - Shamyl, use General/AppKido, it's better IMHO and did not break (anyway the current version is fine).

----

Anyone have a clue why the download size is less than 2/3 of what previous upgrades have contained? Maybe they have figured out something
in the packages. Or is the documentation not being updated?

* Close - they don't include the PDF-based documentation now, just the HTML -- General/TonyArnold *


----

I'm using General/XCode for Java right now, and 1.5 is the first usable version -- my problem is that I'm the only dev using a mac and xcode on the project so all of the build is in external ant files which are now finally really supported -- for instance I can now build in the IDE and see the errors and fix them -- in 1.2 , I needed to ant from the terminal to see where the errors were.

Also, I like the code completion.

-- General/LouFranco

----

Well, Here is a question. Why does 1.5 insist ALL of my header files are resources. They are all being copied durring the build, including new ones I create. Yikes!

----

I've been using Code completion for a while (since 1.2? 1.1??) I like it but would like some improvements. When you get the full list of methods, it includes all superclass methods, too, which adds up to a lot. So I'd either like a way to give it a hint if I want just want methods unique to the class vs all ('cause you know I don't need to see General/NSTableViewDelegate methods when I'm messaging a button), or maybe a search that isn't just from the start of the name ('cause I tend to forget whether it's "insert" or "add" and such amusing things). But it's better than nothing! For those who don't read all the docs/release notes, use cntl-/ to skip through message arguments. I don't understand why Code completion suddenly starts up when I haven't typed a character, such as at the beginning of a line. I think it should always wait for you to type one character unless you press F5 explicitly. (I guess I should bugreport that.)

I find, as with some others, that there are too many windows. Some feel there are too many editor windows. I think they need to unify the editor panes across disparate windows in some way. The debugger window loads files with bugs and then not files you were just working on so you have to switch back and forth. If you open two projects, you get separate debugger and build etc windows for each project -- makes sense but it sure is cluttery. Better window management of some kind. I should just use Expose more but I'm still not used to it.

There is still a stupid bug where old projects open with the split view subviews tiled incorrectly, but they know about it.

I would like true integrated support for Unit testing. As good as General/OCUnit seems, I'm still too lazy to use it because it's not fully integrated into the IDE and has no GUI for the test results. The framework/script approach is just a bit weird.

Generally I'm having really good luck with it and only one bug which would have been bad except for CVS. � General/BrentGulanowski
