---
layout: page
title: UnitTestingProsAndCons
---

What are the pros and cons of General/UnitTesting with test driven development?

Pros:

* Instantly know if a change in the code broke another piece of code. This is very useful when many people are working on the code.
* Helps design by focusing on the interface of objects (what an object does rather than how it does it).
* The tests become the specification.  When you define the tests as part of your design process, you can ensure their completeness.  When they all pass, you're finished.
* Helps when writing the General/ModelLayerFirst because you have no GUI to test the code.


Cons:

* Sometimes difficult/impossible to write (for GUI or Network related code, etc.)
* Requires more code to be written. Although some may argue it is faster, you still must write more code.


For those with experience in General/UnitTesting, please feel free to edit this list. Discuss this list below.

I'd like to encourage people to tell us what they like about unit tests, what they don't like, any qualms they might have, et cetera. I've tried, but not extensively, and I've got a lot of inertia--that's *my* excuse. But I know they're valuable, and I'm pretty sure they could help my development process. How about you?

Pretty much the same here.  They seem like a lot of work to use.  Maybe they would help me, but I have too much inertia working against it. --General/OwenAnderson

*I have wanted to use unit tests since they went pop, I think the idea is good, but it does not work in real-life for my projects, maybe I am just ignorant, but I have tried to formulate some General/UnitTestingQuestions to express which problems I face.*

----

They're not actually that difficult to write for General/GUIs, so long as the GUI is properly decoupled (Model/View, etc).  Networking is a special case, as rigorous testing requires a fairly large test harness to be written (though you can easily do this only once, or license one).

Also, I'm not sure about the "more code" argument, apart from source files being a little larger.  If you define your tests beforehand (design by contract), it's very feasible to end up writing less code **counting changes** than coding from scratch and testing afterwords.  The early definition of the tests requires that your design be thought-out before you start coding, not modified as you're coding.

----

One advantage I don't see discussed/stressed often enough is that they provide an immediate and permanent bug reproduction mechanism when your code does break. No time is spent trying to reproduce the problem. You simply see the evidence in the failed test.

Another pro - tests can also be used for profiling purposes, and help in disovering/prioritizing any performance issues. In this case, you probably only want to consider tests that cover a significant piece of functionality( e.g. calculating a Mandlebrot set ), instead of a unit test that tests an objects get/set methods.

Also - related to the first 'Pro' item above,  unit tests are a tremendous benefit when a large portion of your code base needs to be rewritten/refactored. You no longer question if the major change you're about to implement has broken something somewhere else. Run your tests to find out.

Another con - your level of confidence of your code cannot be any higher than your test coverage for that code.

----

But if you don't have any tests, how can you have any confidence in your code at all?

I think he meant if you're coding to the tests, you risk coding specifically for the tests.
*
If you're doing test driven, the goal is to code specifically for the tests...
*


----

While that statement is accurate, it isn't what I was trying to say. I wasn't very clear.

Actually what I meant was that having all of your unit tests pass isn't necessarily a sign of a good, solid product. It's important to make sure that you're test coverage ( the percentage of units you test ) is thorough.

For example: I was on 'Project A' for a little over a year. There were 8 developers working full-time on this project. We implemented General/UnitTesting and General/ContinuousIntegration, and followed each of those practices strictly. At the end of my time on the project, we had over 1100 unit tests. They took 30 minutes to run completely. The team had a high level of confidence with each delivery. We never had any regressions. Every bug discovered was either a complicated case or a misunderstanding of a complicated requirement. The user was highly pleased with the product, and never perceived the bugs we discovered as being the result of shoddy development work. Every time a bug was discovered, the first thing we did was write a test which aggravated the bug. Then we fixed the test.

These results came about because we wrote tests for **everything**, and never accepted anything less than having every single test pass every time someone checked code into the repository. We gave significant ( good humored ) grief to the developer that broke the build...

On the other hand, I just rolled off a project where - like the first one - developers ran their tests every time code was checked in. They paid attention and made sure that all the tests ran. Unfortunately, they only wrote unit tests for what they considered to be 'major function points' of the application. Regressions were common. The bug list was significant. There were even several cases of developers commenting out test cases just so the build would run. They derived a false sense of security by saying 'well, all the tests passed.'

Unit testing is only as effective as your commitment to writing unit tests. It's also only as effective as your ability to test. Ability improves with effort. Effort, however, is based on commitment, which is difficult to have if you're not convinced that testing is worthwhile in the first place.

General/TimHart

----

I am willing to learn to write `unit tests'.  For know I know a litle bit about Unit tests, and if someone is willing, please write a tutorial or a reasonable sized  How To ( I mean not to small ).

General/RvA
----

The classic paper is "Test Infected: Programmers Love Writing Tests", which can be read at http://junit.sourceforge.net/doc/testinfected/testing.htm.  It uses Java for its examples, however.

----

Assuming you've convinced yourself that unit tests *might* be worthwhile, and figured out how to write one or two, here's some advice from someone who's a huge fan of unit testing...

For what it's worth, I think there are two things that are, or become, essential when trying to write unit tests 'in the large' (on a scale for a real-world application):

  - You use Mock Objects (http://c2.com/cgi/wiki?General/MockObject ), and
  - You need acceptance tests (http://c2.com/cgi/wiki?General/AcceptanceTest ).

The first is what lets you do true unit tests -- meaning that, in each test, you really, truly instantiate and use exactly one (1) class from your production code, which is the class under test. Otherwise your "unit tests" invariably morph from unit tests to subsystem tests to nearly system tests. Ideally (and you really can come very close to this), if you introduce a bug into your production code, exactly one unit test fails -- no less and (this is the hard part) no more. You can even do this with things like network access and databases; you may have to think a little harder about how to model the network or the database in your program, but in my experience this can dramatically improve the architecture of your system anyway.

(BTW: Objective-C is a godsend for mock objects when compared to something like C++ or Java. One simple class that implements forwardInvocation: to record calls (with parameters) made on it, plus lets you set return values, works as a mock object for every single object in the system. In Java you're reduced to doing just disgusting things with java.util.Proxy or (more often) turning every single program class into three (interface, production implementation, mock implementation).)

The second is easier to miss, but just as critical. Each unit test tests exactly one class; each acceptance test tests exactly one feature. These are how you make sure you're wiring together all those proven-correct objects correctly for your program. They're often even harder to write until you get the hang of it, but may well be even more valuable: they will detect wide swaths of errors, show you exactly what you've broken from a user perspective, and (if you write the supporting infrastructure right) never need to be changed -- unlike unit tests, they're dependent not on the architecture of your product, but its *feature set*. (There are even tools like Fit, http://fit.c2.com/, that let you "write" your acceptance tests in HTML. Trust me, it's nowhere near as patently stupid as it seems from that description. ;-)

Finally, if you're more than one or two people, automating your tests is probably also well worth it. I'm not aware of any continuous-integration toolkits specifically for Objective-C (like General/CruiseControl -- and others -- are for Java), but I'm sure you could adapt something or even build one yourself pretty quickly. This lets you have an independent record of the exact state of the software at all times and makes sure you catch problems right away, not in three weeks when you've totally forgotten just what you did. It also can serve as a damn fine QA department. (Humans executing repetitive actions to test computer software is sort of amusing, when you think about it...computers are exceedingly good at doing things repetitively, with perfect accuracy. Humans aren't.)

I do fall squarely in the camp that believes unit tests actually make you go faster (and often a lot faster) -- you pay a little now, and avoid paying the truly huge costs later (the four-day binge tracking down that nasty bug caused by an off-by-one error in some minor class). They also give you such a sense of confidence in your code that you feel fine about re-designing it midstream, adding functionality, and so on. 

-- General/AndrewGeweke

----

I'm new to unit tests and am starting to implement them within a relatively large and complex program. One problem I am running into is writing tests for asynchronous classes. I have several classes that download data (such as XML) and return an array of objects from that XML. I was thinking of writing the tests in two different ways: one would be to allow the class to download synchronously, and the other would be to write a "data provider" class that feeds the main class hard coded XML. For the latter the test would essentially be for the XML parsing. For the former, the problem with synchronous downloading and General/NSURLConnection in Cocoa is there seem to be leaks in the synchronous method. I'd rather not spend time making a synchronous downloader (even if that's possible - I can't figure it out now anyways). Should I just go with the hard coded XML method?

----
An obvious approach would be to use     General/CFRunLoopRun() in your unit test after you set everything up, and call     General/CFRunLoopStop() from your callback once you've verified that everything is correct.
