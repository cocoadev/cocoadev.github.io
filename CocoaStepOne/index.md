---
layout: page
title: CocoaStepOne
---

Go back to CocoaPrerequisites 

Skip to CocoaStepTwo

** Getting Started with Cocoa ** - Apple provides a nice sequence of very introductory steps at http://developer.apple.com/referencelibrary/GettingStarted/GS_Cocoa/index.html.  If you are an experienced programmer, you might be tempted to skip this information or skim through it.  That would be a mistake in most cases.  The introductory material may bore experienced programmers, and the initial trivial tutorial may not seem relevant to an experienced programmer.  Nevertheless, the Cocoa way of doing things is sometimes subtly different from the techniques common with other software technologies.  In a few cases, the Cocoa approach is radically different.  Since the authors of documentation and tutorials cannot assume what preconceptions or education the reader might have, the material has to be pedantic.

Some people new to Cocoa justifiably complain that much of Apple's documentation seems to use undefined terms or defines terms using other undefined terms.  Keep in mind that a common technical vocabulary is needed to effectively communicate.  Apple's choice of terminology comes from a different tradition within computer science than some other popular software technologies.  Apple's terminology is standard for people who come from the Smalltalk tradition which started the whole object oriented programming concept.  The existence of different traditions that have not yet coalesced is a testament to the youth of object oriented programming.  Seminal texts like "Design Patterns: Elements of Reusable Object-Oriented Software (Addison-Wesley Professional Computing Series) " by Erich Gamma (Author), Richard Helm (Author), Ralph Johnson (Author), and John M. Vlissides (Author) have made tremendous progress unifying the field and providing a common vocabulary.

** You need a conceptual foundation before you can make progress ** - In most respects, Cocoa is not an a-la-carte framework.  There are key design patterns, repeated idioms, optional conventions, and most importantly some essential conventions.  No matter how much experience a programmer may have, if the programmer fails to adhere to essential conventions (like Cocoa memory management conventions), they will not succeed with Cocoa.  

Because Cocoa is a cohesive system that is astoundingly consistent, a programmer who groks it can often correctly guess class and method names,  anticipate potential error cases, avoid unnecessary code, and benefit from increased productivity.  On the other hand, a programmer who is missing one or more key concepts is likely to find seeming barriers everywhere because Cocoa probably applies the concept ubiquitously.

So what are the key concepts?

Above all is the ** Model View Controller (MVC) Pattern ** used to organize all of Cocoa.  ModelViewController http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/chapter_5_section_4.html


*** C pointer assignment ** - Programmers coming from environments without access to explicit pointers are often lost immediately when attempting to use Cocoa because every Objective-C object is accessed via a pointer. If you don't understand how and why to use pointers, you should learn the C programming language and ideally at least one assembly language so that you understand how computer Central Processing Units (CPUs) work and how C, C++, and Objective-C generate the machine language that is executed. This points back to the first prerequisite for Cocoa programming: CocoaPrerequisites. Ref: Any C Programming book.
*** Two Stage instance creation ** - This is Cocoa's convention for creation of new instances of Objective-C classes. http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Tasks/AllocInitObjects.html http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_6.html
*** Convenience Methods ** - These Class methods conveniently return already initialized instances of Objective-C classes.  http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Concepts/ObjectOwnership.html
*** Reference Counted Memory Management ** - This was NOT invented just for Cocoa. Reference counting is one of the oldest and most widely used techniques for sharing and keeping track of scarce computer resources such as memory, file identifiers, inter process communication tools like FIFOs? (unnamed pipes), graphics card texture memory, strings (via the CString?)class) in Microsoft's Foundation Framework, COM objects via IUnknown? in Microsoft's COM/DCOM/Active-X, "smart" pointers in the Boost C++ library, and much much more. Whether or not you plan to use Cocoa, any serious programmer should understand this basic and widely used technique.  http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Tasks/AllocInitObjects.html http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_5.html  Simple Examples: http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html
*** The SIMPLE Rules for Reference Counted Memory Management ** - http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Tasks/MemoryManagementRules.html
*** Accessors ** - This is the right place to perform all memory management.  Using Accessors also prepares your code for more advanced Cocoa usage.
*** Encoding/Decoding Archiving/Unarchiving ** - These patterns/concepts are essential for understanding how Apple's critical Interface Builder tool works.  Interface Builder is used to develop the user interface for Cocoa applications.(The following link is obsolete.)  http://www.stepwise.com/Articles/Technical/FreezeDriedObjects.html
*** Copying ** - http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/Tasks/ImplementCopy.html
*** Introspection ** - http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_7.html
*** Mutability ** - http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_8.html
*** Class Clusters ** - http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_9.html
*** Singletons ** - http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_10.html
*** Notifications ** - http://developer.apple.com/documentation/Cocoa/Conceptual/Notifications/Introduction/introNotifications.html
*** Delegates & Data Sources ** - http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CommunicatingWithObjects/chapter_6_section_4.html
*** Outlets ** - http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CommunicatingWithObjects/chapter_6_section_3.html
*** Targets, and Actions ** -
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CommunicatingWithObjects/chapter_6_section_5.html
*** Responder Chain ** - http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CoreAppArchitecture/chapter_7_section_6.html http://developer.apple.com/documentation/Cocoa/Conceptual/EventOverview/EventArchitecture/chapter_2_section_6.html
*** Proxies and Forwarding and Invocations ** - http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/Articles/chapter_13_section_5.html
http://developer.apple.com/documentation/Cocoa/Conceptual/DistrObjects/Concepts/messaging.html 
*** Prototypes (Cells & Controls)** - http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CoreAppArchitecture/chapter_7_section_7.html
http://developer.apple.com/documentation/Cocoa/Conceptual/Matrix/Concepts/AboutMatrices.html
*** Bundles ** - http://developer.apple.com/documentation/Cocoa/Conceptual/LoadingCode/Concepts/CFNSBundle.html
*** Controllers ** - This subject has gotten very large in recent versions of Cocoa. Limit yourself to the following for a while before attempting more advanced and esoteric Controllers. http://developer.apple.com/documentation/Cocoa/Conceptual/Documents/Concepts/WinControllersAndNibs.html http://developer.apple.com/documentation/Cocoa/Conceptual/WinPanel/Concepts/UsingWindowController.html 
*** Decorators ** - This pattern is not emphasized in Cocoa but is used frequently within the framework.  Scrollers, rulers, bezels etc. follow this pattern. http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/chapter_5_section_3.html 


** Wow! That's a lot of concepts ** - Yes that is a lot of concepts. That isn't even all of them. Those are just the ground floor of concepts, and even bigger better ones are built on top. The ground floor concepts should be enough to get you started, and with an understanding of those concepts, you should be able to read both reference documentation and conceptual documentation about Cocoa and not constantly encounter "undefined" terms.

** Some astute programmers might recognize some of those "concepts" as object oriented design patterns ** - DesignPattern - Indeed, they are. Cocoa has some very elegant implementations for common design patterns, but very few patterns are unique to Cocoa. These patterns exist and are used in other popular software development technologies. Mastering these patterns/concepts will make you a better and more skilled programmer with those other software development technologies as well as Cocoa. However, the use and reuse of these patterns is really not optional with Cocoa the way it might be with other technologies.

next CocoaStepTwo - Finding Apple's Cocoa documentation and samples

