---
layout: page
title: CodingGuidelines
---

The document at http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html says about naming instance variables:

*Do not use the underscore character as a prefix meaning that the instance variable is private*

This got me a little confused, because I have started to name all my instance variables with an underscore prefix, due to how KeyValueCoding will search first for the key with an underscore, and next just the key.

So should I stop with the underscores? Often instance variable names will clash with local variables or arguments - normally I solve it by always using title case for my instance variables, but then it will not work with KeyValueCoding, which is why I adopted the other convention...

----

A1: The Microsoft convention is to use a lower case     m to prefix all member variables.  Not the worst convention to use in coding.

A2: Apple reserves the underscore prefix for their own private ivars. AFAIK this hasn't changed - use either a double underscore or a underscore + unique prefix for your own stuff.

*I think you missed the point about using the naming convention to get     valueForKey: and     setValie:forKey: to work (automatically) with my class, without having to write extra code, nor have problems with ivars clashing with local variables or arguments.*

----

Checkout NSKeyValueCoding (item 2 and 3 in     valueForKey:):


*If a public accessor method is not found and the class     methodaccessInstanceVariablesDirectly returns     YES, searches for a private accessor method based on key (a method preceded by an underbar). For example, with a key of �lastName�,     valueForKey: looks for a method named     _getLastName or     _lastName.
*If an accessor method is not found     valueForKey: searches for an instance variable based on key and returns its value directly. For the key �lastName�, this would be     _lastName or     lastName.


This sortof implies that I should **not** prefix my ivars (with other than a single underscore), unless I want to implement accessor methods for them.

----

This doesn't conflict with Apple's recommendations wrt naming - prefix your variables and methods with underscores if you want, but you're running the risk of conflicting with an Apple private ivar/method with the same name. Mostly it means you should write accessor methods for instance variables.

*So how do you interpret the quote above about not using underscores? only to not use it as meaning private? there are other mentions of not using the underscore IIRC � having to create accessor methods for all ivars is often irritating as I often have e.g. a window controller with 10+ ivars which are just proxies for the GUI. I gues in that case naming hte ivar w/o underscore is okay there, because I won't have much code and thus a low chance of local variables (or arguments) with the same name as the ivars (except perhaps the init arguments), but that just leads to heavy inconsistancy...*

I interpret it as meaning there's nothing stopping you from using the underscore for anything you want. But Apple uses it for their own private stuff so you may run into problems there. Be forewarned. The link you gave above also says *If an instance variable is to be an accessible attribute of objects of the class, make sure you write accessor methods for it.* There are many tools that will automate writing proper accessors - Accessorizer and Objective-C Services to name 2 - and once written you shouldn't have to touch them again.

Well, no � no technical reasons, but when Apple writes **don't** and also that **Apple reserves the use of this convention**, then I'd like to honor that, similar to how I don't name my init method     funkyInit, even though I could :) wrt accessor methods, this was true before KeyValueCoding, but with valueForKey: it explictly says it will return the instance variables named after they key, unless disabled by the class. And as said, I have a tendency to create a window controller subclass with maybe 10+ ivars, all bound to the GUI of the controller, so even though tool exists to help me write code for these 10+ ivars, I would rather avoid cluttering up my program with such things, when they are only used for one thing, binding to the ivars, and really are not needed to do that job.

