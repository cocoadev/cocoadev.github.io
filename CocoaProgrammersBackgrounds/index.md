---
layout: page
title: CocoaProgrammersBackgrounds
---

Hi, I'm wondering if I can ask everyone a couple questions. First, how did you learn Cocoa? and second, how long did it take to develop any level of proficiency in it?

----

It's hard.  You're not stupid.

----

I agree, you're not stupid, but I don't think it's hard. That may well be because I've been doing it for a while. I learnt Cocoa by reading the ObjC pdf, then going through the Currency Convertor tutorial three or four times. Oddly enough I never got that simple thing to work, at least not using the suggested model class. I even copied and pasted the code and it didn't work. So I started on something more useful/interesting, which always makes learning a new programming language easier. I can't say when I actually developed a non-trivial level of proficiency in it, and if I could it wouldn't mean much since I didn't always have much time to do Cocoa. I started learning Cocoa in the middle of exams/assignment due dates.

----

Several of the Mac OS X software developers ( http://www.artlogic.com ) at Art & Logic (one of the more prominent Mac OS X software development companies) learned it fairly easily.

It's not easy, but easier than riding a bike. Does that help?

----

Hehe. I also learnt Cocoa in the middle of exams which was a really brilliant idea on my behalf (I can't remember how many I passed, but it was in the low single digits). Proficiency took longer, perhaps 9 months for me (I didn't have all that much time to focus on it) but shaking bad habits from VC++ and VB was the difficult thing. Once everything clicked...well anything was possible. Learning C in my opinion is not a must, but it really helps to know it. Some basic C methods are needed of course, but learning it for 2 years is not required.

----

I got into Cocoa as follows: in Grade 10, I took a computer class which taught me C (mostly). After that I learned C++, partly by taking the Grade 11 computer class in which they mostly taught us the things I had learned the previous year, and partly by getting into MacZoop after figuring out that programming the Macintosh in C was just not gonna work for me.

Around that time, OS X came out, so I found and downloaded ObjC.pdf, and immediately fell in love. We got the public beta, installed it on an underpowered (not to mention borrowed) beige G3 with a mere 96 MB RAM, and soon thereafter I pulled the dev tools over a flaky 56K modem. Considering myself a man for weathering that particular trial, I read ObjC.pdf six or seven more times, and started coding.

Wow, did I ever get in over my head fast. Almost the first thing I was doing was multithreaded programming, something I had absolutely no background with. After that I got really badly confused by InterfaceBuilder and forgot what pointers were or how to write functions (seriously). So I slowed down and started writing bloody heaps of utility code, learning a lot but never testing any of it (not even in a running-app sort of way--frameworks only). Frustrated that nothing was being accomplished, I then started writing apps, and was absolutely thrilled when, as the culmination of ages of work both in learning C and ObjC and Cocoa and in learning about bezier curves, I completed BezierCalc. I was less thrilled when the next thing I did was screw it up very badly, but by that point I had moved onto game development.

Game development led me to OpenGL, which I've been learning inside-out ever since, and to language design, an interest I share with KritTer. It also led me to Lodestone, which sort of emerged out of the attempt to build a modeler for Firestorm. I have a lot of projects underway, but for the moment I'm taking them more or less one at a time, which makes sense since they all sort of build off of one another.

So my advice after this huge spiel?


*Learn C. Get a loose understanding of everything, but especially know pointers well enough to understand that even though you can't see the asterisk, an id is still a pointer and the object it references can therefore be referenced in various places.
*Be ambitious, but not too much so. Be implacable. 
*Launch InterfaceBuilder before you launch ProjectBuilder until you absolutely know you don't need to. Even then do it for a while. Don't start by writing Foundation tools; even if they seem simpler, they're really not. Fun, but come on, speedy results are highly encouraging.
*Live and breathe ObjC.pdf - it is pure gold. I think it's also got a different name than ObjectOrientedProgramingAndTheObjectiveCLanguage now.
*Read and contribute to the cocoa-dev list and this site! Both are brilliant resources, although I personally prefer the site about a millionfold.


Oh, and one more thing. When in doubt, bug someone who knows what they're doing personally. Almost all of us are more than pleased to give our fellow coders a hand. It's contributing back to the community, man!

-- RobRix

----


A day or so (just syntax), few day to get comfortable with the API and memory managment.  But I've worked with Smalltalk (very similar to Objective-C) in the past.  In fact it was my first language,  So the transition over to Objective-C wasn't bad.  And I've used retain counters with other api's so that wasn't too bad.  The Concepts and Good design priciple, however, takes years and I am still learning many new things every day

----

I was a pure linear C programmer with little to no understanding of object oriented concepts.  I was trying to learn Java but without good RAD tools, it wasn't sinking in.  With Cocoa and Objective-C, I was able to learn it in a couple of weeks and became somewhat proficient in about a month.  I started to learn it at about the same time as a friend of mine who had very little programming knowledge (HyperCard stacks and HyperTalk were his only experience with programming - though not trivial).  When he wanted to learn a specific topic, he read through the relevant chapters of a couple of books, used the OmniGroup site, CocoaDev and CocoaDevCentral.  Spent a few hours a week.  Took time off from it occasionally but was able to comfortably grasp it in 6 months.

From our experience, I think the key is to find numerous resources that present similar concepts in different ways.  We perused through the Apple PDF files, the Hillegass book and one of the O'Reilly Cocoa books.  

If you hit a wall in your comprehension, stop for a while.  Take time off and come back to it later.  It seems that sometimes the mind is storing and comprehending information in a certain way that is not "compatible?" with the direction that the information path is going.  When you leave it for a while and come back, your mind is starting down a fresh road and things sometimes seem clearer.

----

I dove in in february 2003, so I'm still learning -- but I feel I'm getting it at the very least. For background, I taught myself c & c++ in highschool and college -- I've held jobs programming (mainly low level c and assembler on embedded devices ) so I've got the basics down.

For the last few years I wrote my code in c++ using the Qt toolkit, but I got fed up with linux -- bought a powerbook -- and then read the ObjC PDF and bought a cocoa programming book from Oreilly (LeaningCocoa). I spent my saturday and sunday mornings/afternoons in a coffee shop working alongside the oreilly book taking each chapter's demo code and extending it until I considered myself to understand. Don't underestimate the power of working in a coffee shop -- it gets you away from TV, family, *playstations* etc; there's no better way to focus on the topic at hand, IMHO. In fact, thanks to airport, I'm typing this in a coffee shop right now.

Then, I dove in and started porting my old linux apps. That's where you really learn -- I had to figure out how to make something which was done in a completely different design model work in a completely new one. 

The language is irrelevant -- the API is *almost* irrelevant (so long as you have all the important references bookmarked). What matters is grokking the new *design*. Once you get the design philosophy of cocoa/foundation down, the rest almost self-assembles.

But then, 99 & 44/100ths of my code is portable standard C/C++ and I'm just using cocoa for GUIs, so YMMV.

--ShamylZakariya

----

I would NOT learn C first.  Learn some other Object Oriented programming language, even if it's simple just to get the mindset.  It is great that new programmers do not have to go through the procedural mindset and then have to make the transition.  I would recommend getting any old learn C++ in 24 Hours book, working through the examples, and then powering through a good Objective-C/Cocoa book.  I would specifically recommend Cocoa Programming for Mac OS X by Aaron Hillegass, it's a great beginners' book.  After that you can go learn C and fine tune your programming skills.  Knowing C will help you in the end, but I probably would not recommend it as a starting language.  Learning about malloc and free are useless since you'll be using retain and release to manage memory.  You'll hardly ever have to get down and dirty with strings or arrays either.  Better to learn good programming design/patterns than some low level details (which you should learn later).

