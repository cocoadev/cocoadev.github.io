---
layout: page
title: DoubleClickingMyFileOpensMyAppHow
---



 If VERY sorry to have to ask this question but I'm a little confused and have searched long and hard to find the answer to no avail.

I have just leaned how to save my my applications data, i have been using the following two ways...


NSData *data = [NSKeyedArchiver archivedDataWithRootObject:mydata];
[data writeToFile:filename atomically:YES]

AND...

rootObject = [NSMutableDictionary dictionary]; 
[rootObject setValue: [eventDetailsModel eventDate] forKey:@"eventDate"]; 
[NSKeyedArchiver archiveRootObject: rootObject toFile: path];


But both create files that are plainlooking and when double clicked dont know to open in my app and open in the text editor.

1) How do i make my file upen up in my app on double click?

2) How do i get the file i create to have an image i specify (so its not just plain looking whilst sat on the desktop)???

3) I thought when using the above ways to save my data, it would be saving as binary, but when i look at the files in a text editor, i see some text which was originaly  contained in an NSString perfectly readable. I thought that saving as binary would provide a small degree of security, say for instance if i wanted to store a password in my file.

----

----
Okay, to answer you're first question, you'll just want to set up the types of files your application can handle. You do this by editing your target settings in Xcode. Just go to Project > Edit Active Target 'foo' and then look under the Properties tab. Under Document Types is a list of editable types that your app will be announcing it can handle, so the Finder and everything will expect it to be able to open them.

For your second question, you just specify the icon in this same Document Types list using a file from your app bundle. The image MUST be in your app bundle to work. It must be distributed with your app and everything.

Third, and most importantly, using NSKeyedArchiver and NSKeyedUnarchiver does not guarantee ANY security. It is just the way to convert normal objects (NSString, NSArray, or anything NSCoding-compliant) to a property list for storage as a file. There are other ways to save data to disk, such as CoreData or another SQLite store. You might want to look into something more secure, because the way your app saves and loads its data is where the security will come in.

Hope this all helps! --LoganCollins
----
----

----
Thank you very much, and sorry for the newbie questions.
----

----
Hey - this is THE PLACE for newb questions - that way we can read it all after the fact all pretend we 'knew it all along' :-P
----
Can we use store facility of CoreData or SQLite in non-CoreData applications?? 
----
You can use SQLite from any application, though it may be a little daunting if you've just gotten the hang of NSArchiver. I don't see why you'd want to use SQLite, though, unless you need relational abilities.

