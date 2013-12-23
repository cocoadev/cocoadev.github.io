---
layout: page
title: NSDocumentAndTheCommandLine
---



I have noticed that it is possible to launch, say, TextEdit from the command line _without_ using the "open" command:
/Applications/TextEdit.app/Contents/MacOS/TextEdit   someTextFile

However, if I use my own Document-based application, say "./myApp.app/Contents/MacOS/myApp someTextFile", I only get a blank window titled "someTextFile"
Using "open -a myApp.app someTextFile" brings up someTextFile correctly

How can I get my application to open correctly in either case?  I am currently only overriding "- (BOOL)readFromFile:(NSString *)filename ofType:(NSString *)aType"
Should I override something else?

I'm asking this out of newbie curiosity, to help me understand the NSDocument architecture better.

----
TextEdit is not an NSDocument application. Its source code is available in /Developer/Examples if you want to see how it implements it.

----

Thanks for the tip.  I had a look at the TextEdit source, and came across "- (BOOL)application:(NSApplication *)sender openFile:(NSString *)filename".  So in my application, I created a MainMenu.nib delegate, called AppController, and implemented the above method in AppController.  This worked!

But...just out of curiosity, I removed the method, did a clean all, recompiled, and it still works.  This is puzzling me.  Was a delegate all I needed, even though the delegate now does not have any code?  If so, why?

