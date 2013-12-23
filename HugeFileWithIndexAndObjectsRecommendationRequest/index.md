---
layout: page
title: HugeFileWithIndexAndObjectsRecommendationRequest
---

Hello

We developing a app like a workgroup app ( its not but it looks like ). The file will be on each laptop cq desktop and syncronized with a server where the users can connect at. There are a lot of mobile users. Because of this there is no option to use a central db.

Because the file's can grow huge, think about ,5-5Gb and do have 10000's small object. This is because there will be lets say 1000 users adding information, connect and sync with the server. I like to sync only the changes. If every user updates 5 objects, then to replace everytime the whole file is ridiculous, so we cant use CoreData. Also it ( NSKeyedArchiver ) was to slow and data is readable. At first we where using ower own vision of the idea of NSArchiver and came up with something like MAKeyedArchiver from Mike Ash.

What I really look for is that we can use an unix kind of index/offset table e.t.c pointing to the objects in the same file ( no wrapper, because some users would run also windoz on it and they will see the inside of this wrapper ). How do people combine objects into a unix type of file? Is the best way to cenvert the object into a structure? But what about NSString encoding? Does some one has experience with this and have some pointers so I can look at it.


Thanks

SC

----
Use a database. For the love of all that's holy, use a database! That's what they're for! SQLite sounds pretty much ideal for this.

----
Thanks for your reaction but, this is not a option for this app. We did look for this already. And what we want is a single file database controlled by our own app.

SC

----
If you are intending on supporting Windows clients that alone is reason enough not to use CoreData, NSKeyedArchiver, or anything Cocoa provides.  If you use Objective-C at all for the Mac Client, you'll have to do the same work in a different language for the Windows client.

----
Um, what? What exactly is SQLite if not a single-file database controlled by your own app? Seriously, your requirements say "database". You are not going to find an alternative which is not a database. You may be able to find a suitable database which is not SQLite, but I see nothing to indicate that SQLite would be anything other than an excellent choice.

----
Please, SC says SQL is not a option. Respect this. If someone is willing to help her/him, please help with answering the question.
There was a clear explanation what (s)he wants.

We also have some similair requirements and also a SQLite db is out of the question. F.e one of our requirement was f.e at less as posible depended of Apple's changes between osx versions. Maybe this is the same for them.

----

The poster seems to have some misconceptions, though. "If every user updates 5 objects, then to replace everytime the whole file is ridiculous, so we cant use CoreData. "  If you use CoreData with the sqlite backend, updating 5 objects will not require rewriting the whole database.  Only 5 rows will be written.  

----
SC says SQLite is not an option but SC does not seem to know what SQLite is, as evidenced by his implication that it does not fit his requirement of a single-file database controlled by his application. If his rejection is simply due to misunderstanding what SQLite is, it is not only our choice but our obligation to correct him.

There's this whole bizarre idea that we should never provide opinions or advice that is not asked for. That is ridiculous. If you want a yes-man who will never ask interesting questions and only do exactly what you tell him to do, I'd be happy to send you my rate card (although that situation doubles my rate).

----
Some times we, developers, are stuck with a design direction and exsisting things. And my question, maybe not clear, is a technical question. My English could be worse to read. I never wanted a discusion starting about yes/no sql. Why, to the last poster, sounds you aggressive, 'I will send you my rate card', what did I do, or, what did the other people do who answered the questions. 

Please let us stay on topic, I need the help of the people here. Just want to know some explanation how to write objects into a normal file with f.e structures and a table pointing to those added or replaced objects. B.t.w it a mac only App.

Thanks

SC

----
sc. Yoe can look at the doc's for NSFileHandle. You can set the filepointer to anywhere you want and at that point you can write your object packed in a NSData. So basicly you could write you header, table, structures and Pointers at the place you want with NSData. I think you know that, you should keep the pointers and the NSData ( object ) size in control.

Good luck

----
Thanks, this works nice, and, I can do write binary data in the beginning and add Objects NSData anywhere I like. I did start checking this this morning. But again thank you.

Running some size tests, why if I use NSFileHandle's availableData I can load only around 268Mb and has to use a loop to get the whole bunch of data?

If I use NSData *data = [[NSData alloc] initWithContentsOfFile:gigaBigFile]; then I read everything, but a litle slower. The test was about a file with a size of 1,5Gb.

If I read file sizes like 2,1Gb or bigger with NSData *data = [[NSData alloc] initWithContentsOfFile:gigaBigFile]; then I get:

ReadLargeFileWithNSData(2953,0xa000ed98) malloc: *** vm_allocate(size=2147225600) failed (error code=3)

ReadLargeFileWithNSData(2953,0xa000ed98) malloc: *** error: can't allocate region

If I use NSData *data = [[NSData alloc] initWithContentsOfFile:bigFile options:NSUncachedRead | NSMappedRead error:&err]; I get len == zero back.

Someone a suggestion?

Thanks

SC

----
On 32-bit Mac OS X (which is what you're limited to with Cocoa for the moment), you only have about 4GB of address space to play with. about 0.5GB is used just by starting your app, and the rest is fragmented, meaning you won't be able to create an NSData larger than about 2GB (and you *must* make sure that it's destroyed before you try to make another one).

If your goal is to use really big files, don't mmap and don't read the entire thing at once. Read little pieces at a time.

But then again I start to wonder, if you know so little about the basics of file reading/writing (please don't take that as an insult, I'm just observing that you don't know a lot right now, not that you are stupid or willfully ignorant), I'm not sure how you expect to be able to create a database substitute. And again I am puzzled as to why you insist on writing your own database instead of using something which already exists. The end result will be the same (except your own database will be slower and have fewer features and more bugs), so what requirements do you have that don't allow this? Do your requirements say, "Must not use any third-party database libraries"?

I sound aggressive because I get really, really tired of being told I shouldn't give what I think is good advice just because a poster has stated that they're looking for something else. If you're asking for advice, then by definition you don't know everything about what you're trying to do, and maybe that means that your stated requirements are incorrect. SC, I'm not angry at you, please don't misunderstand. I'm angry at the person who stated, "Please, SC says SQL is not a option. Respect this. If someone is willing to help her/him, please help with answering the question." The idea that this type of suggestion does not qualify as "help" *angers* me. *If* SQLite were in fact the best choice for the job and the poster's aversion to it is based on incorrect assumptions, would it not be helpful to suggest it? It sounded (and still sounds) to me like that was the case, which is why I pushed it.

----
Since this has tended to one, we should at this point SignArguments, to help those following the page. (Pseudonyms are fine, provided they're consistent through this page.)

I too am curious why you cannot use SQLLite; the reasons above do not adequately explain the decision. Is this an arbitrary decision handed down from on high? If so, it may be in your interest to argue with them even as you code an alternative, and to design your code so it would be easy to switch in a database when those in charge see the light. Or is it perhaps a licensing issue? -- KritTer

----
Thanks for your additions. I did overlook about the 2Gb limit in the docs. Actualy I did read it, but remembered it as 4Gb. And I did read the http://developer.apple.com/macosx/64bit.html page. A doc wrote something about there counterpart tollfree replacment CFData that this should handle bigger sizes. Cant find it for now.
Also I notice that in some places ( during dbg ) there was a unsigned int used instead of int on Panther. But this is no issue anymore because the app will run only on Tiger or later.
Yes I am fairly new to cocoa, and I like it ( not to be new , but about cocoa :-) ). But after few test application I notice that coreDate has to grow more in his current carnation.
Apple is removing bugs in their design and changing to much between releases. We can't be depended on thirth parties. This counts also for SQLite. Like I mentioned before.

To come back on some remarks at other people. I didnt feel any bad feeling about his writing, and you shouldn't feel angry about this to. And he did gives me a good direction and what he wrote, it works great and faster then sqlite. Specialy with small amount of objects each save. So to tease you, I give him a good rate :-P. :-) <Keep smiling ok>I understand his meanings because to many times people keep going to say something thats not ask for. If your manager ask you to do something or there is a company policy, is it appropiate to argue? If they ask for a report of conclusion, yes, put it in the report. We did test things in sqlite, and it was not good enough. I hope the sql thing is a closed case. Anyway thanks for the concern about this.

And!! :-) we already did wrote, and thats where objects are for, wrappers so we can replace the db engine if we wanted to use other db's. To be honest, thats more because of, that we can change different test db engines, we wrote for testing, to let other things keep working.

To come back on topic
Why is there a differences in limits in the different methods in the NSData Class or where is it documented?
Does someone knows? It is 2^28 with 'NSFileHandle's availableData'. But in other methodes it look like its depended on ( to many ) diiferent things.

About the vm failure! The file size is smaller then 2^31. And thats strange in my feeling. I do read the data in chunks, but with NSFileHandle availableData they are to small. It should run for the next decade with small updates but not heavely changes. The db wil only grow and grow.

We dont have a intel or G5 machine, but we are looking to get Intels. If we compile with the 64 bit compiler settings, will this issue not be there on the intels? Someone?

Thanks

SC

----
Did you try NSFileHandle's readDataToEndOfFile this should read up to UINT_MAX ( 4294967295U ).
To clean things up, did you use pools or test with zone's. For the latter I dont have much hope.
And thanks for your rating. :-). It was never my purpose to irritaie, so my apologie.

----
You need to understand what a process's address space is, and what it means for you.

A process's address space on Mac OS X 32-bit (what you're using if you're doing Cocoa, no matter what OS revision or CPU you run on) is 4GB. This means that you can have at most 4GB of stuff that has pointers pointing at it. This includes NSData, strings, loaded code, etc. Furthermore, stuff like NSData must be contiguous (no holes), which further reduces the space available for them. Worse, your address space gets more fragmented (more and smaller holes) as time goes on. In short, there is no definite maximum of the largest NSData you can hold. As a general guide, you should expect anything over 512MB-1GB to fail regularly.

If you have files smaller than this then you can just read/mmap the entire thing and be happy. If you have files larger than this then you should just read the piece you're interested in. This does not mean you read 256MB and then find the object you want. It means you find out where the data you need is stored, and then you read just that data (if your data is 100 bytes, seek into the file and just read 100 bytes). It is unnecessary to read a GB of data unless you actually need to touch all of that data at once, and I doubt you need that.

----

Just use SQLite already.  It's intended for exactly the problem you're trying to solve.  Stop arguing with us and start arguing with your management to use the right tool for the right problem.  After all, your clients aren't paying you to dally, they're paying you to solve their problems.

If you don't want to depend on the version of SQLite that Apple ships, SQLite is public domain and you can compile a version of it into your application that you have COMPLETE control over.

Be aware, though, that when Apple supports an API like Core Data or SQLite you can depend on compatibility from one release to the next.  Your argument that "too much changes between releases" is ENTIRELY bogus.  Apple will not release a version of Core Data or SQLite in the future that is incompatible with correct code that is already deployed; to do so would be just stupid.  The fact that you're making this argument shows how little you know about the platform vendor.  How did you get a gig writing software for a platform you know next to nothing about, when there are plenty of people who do understand the platform and can do the work?

----
*Our is not to reason why. Ours is but to congratulate them for getting the gig in the first place and try to help them be successful.*

I also think SQLite seems like a good choice. But I will even go one better than that. What you describe seems an awful lot like a regular 'ole filesystem. You have a single file requirement? Make a big disk image. I am being quite serious about this. You can just use a filesystem (for now) and work on your business logic. Get something built, that works, that you can show off. Chances are, the performance will be just fine. If it isn't, all you have to do is optimize. Then you can spend as much time as you want with file pointer offsets and locking. In the meantime, you have a working product.   

----
*How did you get a gig writing software for a platform you know next to nothing about, when there are plenty of people who do understand the platform and can do the work?*

Perhaps he was less offensive? Just a thought.

----
In any case, the "plenty of people" do not seem to exist. There are plenty of people who talk the talk, but there seems to be a general shortage of people who can walk the Cocoa walk.

----
sc. Clearly you can't read the entire file into memory. Yet you should still be able to use NSFileHandle as a previous poster suggested but use offsetInFile to go to the right place, and then only read/write the data you need, and you should release it as soon as you're finished with the chunk - otherwise you'll still run out of memory.

Finally, if you have experience with unix files (as you partially hinted at the very start), then you can resort to the low-level unix APIs - and use NSData to build Cocoa objects from the bytes - yuck.

To the rest of you - chill out!

----
You can make a folder **look like** a single file by turning it into a bundle, without needing a disk image.

Also, if you're doing tight loops of autoreleased memory allocations, you may be interested in NSAutoreleasePool, if you haven't already seen that trick.

----
About NSFileHandle. What you are describing is what I do now. I use NSFileHandle ( or where needed a extracted FILE handle ), read the 'some' parts into different NSData parts. Use the NSData_collections, which are collected through formula's, to get and select pointers to the real NSData_data, very vast. In the background I do update this collection with the latest NSData_data. Writes the NSData_Collection and their NSData_pointers back to disk. And I am done.  reasonable safe and fast. If it crashes or whatever, I lose nothing ( or only one object at max. ). I did look for better, but I couldn't find it. I use this mechanism for reading, writing, compacting, syncing AND repairing. With one piece of code. Smaller then sqlite. And can us it at the server without any code change. With also this mechanism I only need to sync small parts of the file with the file server ( only real data ). This happens with at , start/ stop app, when the user want it now and with a timer and also checks the network traffic. Every client wil get the feeling it keeps going without overloading the server at specific and busy times at the day.

What I also want to implement, looking into this right now, is, if when there are several NSData_data sets close together in a row ( sorting pointers )  I want to read them all in one chunck and split them in memory into two or more needed objects. This works well when there is less disk and less file ( data set part ) fragmentation. Maybe server only useable.

About the comment *"Make a big disk image."*. We have less experience with this, but we do test the code with a file path to a HD and a file path to a diskImage. We want to use this as an initial data setup at a clients laptop. Also I am curious if there is a posibility to access a disk image without mounting it? Is it?

Another question does some knows how to create a defragmented file. Now we use a 4 gb DMG and move small files arround, delete half the file content and then we copy our data file into the dmg to get a defragmented. Maybe does someone wrote a script/app for this to get this done on a disk.

Bundle are visible under windows, and then users can look inside and move things arround by excident or confusion. And thanks for the tip. I am using the autorelease pools, but I did wish that they could live next of each other instead of above each other. I have more profit with zone's. And I need definitly optimizing both later on.

One other thing is encrypting the NSData object, does someone have a good read about this, or experience? Not just a man page.

Thanks

SC

----
Wait...you actually got NSZone to work? I thought Apple had disabled that code?

----
If this is true, and , I did read just the NSZone page, then I feel .... Because I did use it blindly, and expected that it just works. Is there any tech note about this from apple?

----
It should be pretty easy to check: just output the addresses you're getting off two different zones when you interleave allocations from them. If zoning is working, the addresses coming out of a zone should be contiguous.

----
NSZone<nowiki/>s are entirely useless anyway, because they don't support a "destroy everything in the zone" operation. When you destroy an NSZone any objects allocated just move to the main zone. All they're good for is attempting to optimize locality of reference, and as we all know the first rule of optimization is "don't do it".

