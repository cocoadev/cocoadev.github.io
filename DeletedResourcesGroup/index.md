---
layout: page
title: DeletedResourcesGroup
---



Hello,

I finally completed my project! I was fiddling with different .icns files to add to my project. I (thought) I highlighted a useless .icns file in my Resources group and deleted the Files & References from the project. But it seems the entire Resources group was highlighted!!!

Nib file, images, plist gone.

Does anyone know of a way to get these back?? My project window is still open. The Edit>Undo menu item is grayed out. I did (unknowingly) save since I last deleted the Group.

----

http://developer.apple.com/documentation/DeveloperTools/Conceptual/XcodeUserGuide/Contents/Resources/en.lproj/01_04_pr_add_files/chapter_7_section_5.html

----

That is what I feared. 

----

If you just deleted the references, you can make a new group called Resources, then find the files on disk and just drag them back in.

If you deleted the files too, then you're doomed. This is a big reason why you should use VersionControl.

----

I did delete the files too. But I was able to get pretty much all the files back!!  I had built the application before, so I had a working .app package. That package contains the resources files it appears. It was a bit outdated, but still good enough.

I will definitely look into VersionControl. Thank you.

----
I too, have in the past deleted a container which regarded itself as selected after I had only selected part of its contents. This is probably a part of the Apple UI / HIG when using Tree widgets. I too, have learned to be careful. It is a shame that XCode allows itself to do filesystem related operations, even when asked. If there were a preference to remove the "Copy files in" feature and its obnoxious dialogue; and the "Delete Files as well as References" (I would give odds that this function has never been used for its stated purpose), from XCode I would enable it as I would any other "Please un-break my app" preference. iTunes handles this with a 'trash' and Spotlight by not doing deletions. If anyone reading this is responsible for the UI of their app, kindly search for these words "Effective interfaces do not concern the user with the inner workings of the system. Work is carefully and continuously saved, with full option for the user to undo any activity at any time", and follow all applicable pointers on that page

----
Just for the record, I use "Delete Files as well as References" quite often; in fact I rarely just delete references alone. I think a major fix for this would be to simply move the deleted files to the Trash instead of wiping them out altogether. However, stupidity is not limited to Xcode and an errant command in Terminal or somewhere else can just as easily break your project, so use VersionControl.

----
O.K. Help me, please. What is the "use case" for doing source file deletions through XCode. I can understand XCode removing intermediate built products and temporary files - it created them and could re-create them if needed. Why not use the finder to move and remove files? Why is it better to use XCode? My problem is that I see XCode as maintaining a database or list (of ingredients) and having a an internal set of rules to do its thing; rather like a chef in a kitchen. If I want no nutmeg in my spaghetti bolognese, I tell the chef to take it off the list; if I want no nutmeg in the kitchen, I get someone to put it in the trash. These seem to be totally separate concepts, and I don't see why they are intermingled. (I would be happy if the delete files option were only only in the menu or context menu and the delete keystroke simply cleared the reference). Perhaps XCode ought to have an automatic subversion repository for every project, and do a checkin on each quit.

----
I have two possible answers:

*You're using revision control.
*You have a custom Makefile that refrences the result of a $(wildcard).


Though I agree moving to the Trash is a great idea.  Maybe someone at Apple did this once too often and we'll find this in Xcode 3... :-D.

----
I'm the weirdo from up above who actually uses the "Delete Files" option. My main use case comes when I create a file by mistake. In a single quick operation I can remove the file from the project and delete it off the disk. Without this, I would have to reveal them in the finder, drag to the trash, go back to Xcode, delete from project. This is not that hard and I wouldn't really miss the feature if it went away, but I do use it now. I also use VersionControl religiously.

IMO anyone watching this page thinking, "Hmm, VersionControl sounds neat but it seems like a lot of trouble, let's just remove this thing from Xcode" should really check out VersionControl. It does so much more for you besides saving you from this one mistake. I can't imagine programming without it now, it's too scary.

