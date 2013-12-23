---
layout: page
title: InheritanceMultipleInheritanceAndCoupling
---

**Inheritance, Multiple Inheritance, and Coupling**

The goal of object-oriented programming is to maximize programmer productivity by simplifying software development and reducing lifetime software maintenance costs. The principal technique used to achieve the goal is object reuse. An object that is reused saves the programmer 
time because the object would otherwise need to be re-implemented. Object oriented programming is a packaging technology designed to promote reuse of software.

*So how is reuse promoted?*"[1]"  There are lots of techniques, but the number one **enemy** of reuse is *coupling*.

As a general design goal, coupling between objects should be minimized. Coupling refers to dependencies between objects. Whenever such dependencies exist, they reduce opportunities for reusing the objects independently. Coupling also applies to subsystems within large systems 
of objects. It is important to look for designs that avoid coupling whenever possible. **All of the Cocoa design patterns exist in part to limit or avoid coupling.** For example, the Model-View-Controller (MVC) pattern is used throughout Cocoa to organize subsystems of classes and is applied to the design of entire applications. The primary intent of the MVC pattern is to partition a complex system of objects into three 
major subsystems and minimize coupling between the subsystems.

To look at it another way, it is important to use designs that accommodate changes through the life cycle of a software system. Designs that are too inflexible ultimately restrict opportunities for reuse. In the worst case, no reuse occurs because it is easier to redesign and re-implement a system than it is to make changes within an 
existing rigid design.

One way to design for change is to limit coupling between objects. If there are few dependencies between objects there is a good chance that drastic changes can be made to one object without affecting the others. It is also possible to anticipate certain types of changes and accommodate them in a design. For example, Cocoa's Delegates pattern provides a mechanism for one object to modify and control the behavior of another object without introducing coupling between them. It can be said that all of the Cocoa design patterns exist in part to accommodate change. That is just one of the reasons that Cocoa is so flexible.

It cannot be said enough times that coupling is the enemy. It is ironic that inheritance is simultaneously one of the most powerful tools in object-oriented programming and one of the leading causes of coupling. In fact, there is no tighter coupling than the relationship between a class and its inherited super-classes. Cocoa is designed to reduce the need to use inheritance. The general rule is that when there is a choice between using composition and using inheritance, use composition.

Having said all that, sometimes there is no substitute for inheritance and it is an essential and valuable tool.  Just realize that it is only one of many available tools and it should not be the first choice every time.

Multiple inheritance has the potential to exponentially increase the coupling between objects in a design.  I won't say that there is never a time to use multiple inheritance.  However, whenever someone wants to use multiple inheritance in a design, I am immediately suspicious.  In my experience, almost any amount of redesign is warranted to avoid the use of multiple inheritance, and in almost every case there are great 
alternatives including first and foremost patterns like composition, delegates, clusters, message forwarding, etc.

Note: In C++, inheritance of pure virtual abstract member functions is approximately equivalent to Java interfaces or Objective-C protocols and is not considered to necessarily increase coupling.  Multiple inheritance from multiple deep hierarchies of non-abstract classes should be avoided.
----
[1] Comment: *So how is reuse promoted?*
If a particular kind of problem has been solved using an object oriented approach, a similar problem can usually be solved by reusing part of the prior problem's solution.  One of the best examples of practical re-use ever produced is Apple's Cocoa frameworks.  Cocoa and its predecessors Openstep and Nextstep have been widely recognized for their excellence and reusability.

Cocoa uses several techniques to achieve reusability.  Some of the most prominent techniques result directly from the use of Objective-C as the implementation language for Cocoa.  Objective-C is an extension of ANSI C which means that existing C code can be easily reused in Objective-C applications.  In addition, compared to other object oriented programming languages, Objective-C is very dynamic and directly supports the ideas of loose coupling described in this article.  See Apple�s own explanation for why Objective-C is used to implement Cocoa: http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/WhyObjC/chapter_2_section_1.html.
Less dynamic languages typically require extra code and more complicated designs to achieve loose coupling intrinsic to Objective-C.  For reference, see the Command pattern applied to C++, the COM, DCOM, CORBA, SOM, IDL, and IUnknown technologies used with other languages to approximate message sending as implemented by Objective-C.  See also signals and slots in Qt: http://doc.trolltech.com/3.3/signalsandslots.html  Qt uses a pre-processor (other implementations use C++ template meta-programming) and several tricky classes to simulate the target/action design pattern used by Cocoa and enabled by Objective-C selectors, Objective-C dynamic message dispatch, and features of Cocoa's NSObject base class.

Another prominent contributor to the reusability of Cocoa is the Objective-C feature known as Categories.  ClassCategories Categories enable programmers to add methods to existing classes even without access to the source code of the existing class.  This often enables reuse without subclassing.

- Erik M. Buck 

----
Like most object oriented languages and frameworks, subclassing is an available technique for reusing existing objects/classes. PitfallsOfSubclassing NewbieQuestionAboutDelegates 

----
[Ref Dr. Brad Cox work on "Software ICs"]

Position Paper
Brad Cox
5th Annual Workshop on Software Reuse
November 18-22, 1991
Center for Innovative Technology Building
Herndon, Virginia
http://www.cs.umaine.edu/~larry/latour/WISR/wisr4/proceedings/ascii/cox.ascii
----
Biography: http://en.wikipedia.org/wiki/Brad_Cox
----
Announcements:
http://www.old.netobjectdays.org/mirrors/stja.cd/Keynote_Tutorial/BradCox.htm
----
OOPSLA 1993
http://delivery.acm.org/10.1145/270000/260319/p55-seidewitz.pdf?key1=260319&key2=2048043811&coll=GUIDE&dl=GUIDE&CFID=22879212&CFTOKEN=19118699
----
Transcript: SOAP: Service-Oriented Architecture and Programming, Pt. 1
MSDN TV
Don Box
http://msdn.microsoft.com/msdntv/transcripts/20030827SOAPDBTranscript.aspx
----
IEEE Software Engineering, 1994. Proceedings. ICSE-16., 16th International Conference on
Volume , Issue , 16-21 May 1994 Page(s):271 - 272
http://ieeexplore.ieee.org/Xplore/login.jsp?url=/iel4/981/7343/00296788.pdf?arnumber=296788
----
Bertrand Meyer on Brad Cox and Software ICs in 1999
IEEE Computer Society
----
Correspondence with Dr. Brad Cox regarding Objective-C and software ICs 1987
http://groups.google.com/group/comp.lang.c++/tree/browse_frm/month/1987-10?_done=%2Fgroup%2Fcomp.lang.c%2B%2B%2Fbrowse_frm%2Fmonth%2F1987-10%3F&

