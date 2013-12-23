---
layout: page
title: BeginnerQuestions
---

**
We were all beginners once, remember? Play WikiWikiNice lest you may need help someday and no one will be around to help you.

So, too, be nice when answering questions from beginner Cocoa programmers (or wannabes :) on books, tutorials, coding and when to shut up and stop asking such stupid questions. See also, HowToProgramInOSX. To kick things off, a well-handled example:
**

----

I'm so new to this whole cocoa programming thing, and in fact to programming in general that I don't even know where to start! Whenever I look for help starting programming in cocoa, I find tutorials that assume you already know everything there is to know about c or java or whatever language they think you should use. If I instead start looking for programming in c or java, I find content totally unrelated to cocoa! Little help?

----

I suggest learning C first. Cocoa is based on ObjectiveC, which is itself a superset of C, so if you don't know C you need to learn it all at once, which would make for a rather long book. You should find you can easily compile C apps with ProjectBuilder. -- KritTer

----

I agree with KritTer, learning C is a good start. A book that on C that I found very useful (well I must admit it's the only C-book I've read :) is:

Learn C on the Macintosh (2nd Edition)
by Dave Mark
ISBN: 0201484064

Contrary to the title it doesn't really teach you to programming your mac but rather teaches you pure C. There is some information that is mac-specific but it's old and since you'll use Cocoa it doesn't really matter anyway. Good luck! - Gabbe

----

hey guys it's me again....
if, hypothetically, I wanted to get my app to see if a file exists how would I go about doing that?

----
Well, I'm assuming you're learning straight C, cause in Cocoa it's fairly straight forward. If you are using FILE and fopen(), etc to handle all your file I/O stuff in C when you using fopen() for example as follows:
    
FILE *file;
// ...
file = fopen(filename,rwmode);

... then file will be NULL if it can't open the file for the mode you choose. This will check for the file's existance if you choose "r" for your rwmode, but it will also return NULL if it can't open the file for ANY reason, IIRC, be it that the current user doesn't have permission to the file, or it can't be written to because it's already in use by another program.

Someone will probably enlighten you to a more specific way to check just for the existance of a file, but the functionality gained by checking file against NULL might be ideal for your situation.

--KevinPerry

----

As far as far as finding a file goes, what exactly do you want to do?  Do you want to bring up an Open file panel and have the user find a file?  Do you have a particular file that you would like to check out without the user's help?  Please clarify.-- Alexander

----

sorry, I am trying to do this in cocoa, and, no, due to my own lack of experience, it doesn't seem too straight-forward.
I'm writing an app that will test for a file at a fixed path when it starts up, say

~/Library/SomeApp'sFolder/someresource

so that if it doesn't find it, the app will create it and if it does find it, it will load the file.  There's probably an easy and obvious way to do this that I just haven't thought of, thanks!

- Murph E.

----

[[NSFileManager defaultManager] fileExistAtPath:@"foo"]. Optionally you can add isDirectory:(BOOL) which will allow you to check for a directory via this code:

    

NSArray *subpaths;
BOOL isDir;
NSString *fontPath = @"/System/Library/Fonts";
NSFileManager *manager = [NSFileManager defaultManager];
if ([manager fileExistsAtPath:fontPath isDirectory:&isDir] && isDir)
    subpaths = [manager subpathsAtPath:fontPath];



Reading the functions in NSFileManager is probably what you will want to do. Hope this helps. -- MatPeterson

