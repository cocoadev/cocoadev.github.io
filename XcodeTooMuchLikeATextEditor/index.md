---
layout: page
title: XcodeTooMuchLikeATextEditor
---



I think Xcode is far less user-friendly than it should be, dwelling too much on your ".h" and ".m" files as text files, working like a text editor and compiler, instead of treating the units of your code separately. I think it could really become a more intuitive, visual, simple-to-use and powerful application.

I think that developers working on projects basically follow a workflow somewhat resembling this:

� They design classes, sketching their main functionality.
� Then they follow these steps, one class at a time:
� 1 They decide on the instance variables and methods their classes will use.
� 2 They create the classes, declare the variables and the methods.
� 3 They start implementing the methods
� After all methods are implemented, they move to the next class.

I end up *manually* navigating and editing not just one, but two text files per class.

I wish I could just select menu items like "add new method," fill in a form (class vs. instance method; return type (pop-up list or combo box); parameters)? Xcode would add the right lines to the right files, like the way General/RealBasic does it. General/InterfaceBuilder (IB) can already do some of this, though only with General/IBAction methods and General/IBOutlet variables. It will also not let you actually edit the code.

But in Xcode, when I create a new method, why can't I bring up a new window, just for my single method; "selecting" a method implementation merely scrolls the window to the line of text where it begins. What's the use of being able to see my *entire* class implementation all at the same time?

Could developers benefit from an interface where you'd have a hierarchical listing of classes, variables and methods, and you could click on a plus sign to add new ones, double-click on methods or variables to bring up windows to edit each, and even letting you rename classes and variables? Xcode would then do all the filing and hosekeeping.
**Just think of a document-based app: you start with two new classes you'll have to rename manually -- in about eight different places. Why not have Xcode renaming them everywhere for you?**
All it would need to do is reference all your symbols by unique identifiers (and not their names) as you develop your code. If you change the name, Xcode could still understand the symbol, and propagate the new name throughout your project. Even General/FileMaker can do that! (Sure, I'm aware of the complications, especially with code reuse, that come from such name changes. However, there might still be a need to rename symbols as you're developing a project.)

A friendlier approach would let you focus on your real job, instead of housekeeping. You'd be navigating between classes, methods and variables in nice browser windows, double-clicking each to do some editing, rather than scrolling through long text files and copying and pasting method declarations and variable names.

Incidentally, the nice graphics are already there. There are blue methods, green variables, and so on. It's just that they merely serve as bookmarks to your header and implementation files, rather than taking a bolder step and let you view them as "things" by themselves, which just *happen to* be part of those two files.

Andr�s Puiz

----

Bookkeeping activities end up being very minor parts of my day-to-day programming. Xcode can't really help with design; the actual coding is where the time and effort goes for me. Once projects get beyond the initial first stages, refactoring support would be nice, like General/ObjectiveCeeRefactoringProject. It'd be nice if there was a code database behind things, like the Dylan project, instead of text files. 

A **huge** amount of effort and research has gone into developing development interfaces.  While some things catch on (syntax highlighting, auto-indentation, class browsing), a lot of other things haven't been as widely adopted; visual class representation is often one of these things (though it is enormously helpful in large projects).  Emacs, which is nominally a "terminal" application, has all the browsing features you mention above, and various other environments have auto-completion for classes and methods.  Not many implement the 'new methed as form' style of programming, because programming with the mouse is inherently more inefficient than typing.

Is it really just the editor that's standing in the way of beginners becoming better programmers?  My experience teaching programming leads me to say no; the people who have done really well are invariably the ones who grasp either the programming paradigm in question (whether OO, logical, or functional), or who have an understanding the actual implementation of the language (like car and cdr in Lisp, or what a virtual method is in C++). If you consider these to be a high-level and a low-level understanding of the language, neither is particularly facilitated by the editor. Students who rely heavily on editor functions in place of this knowledge tend to produce code that's much worse. Making the language friendly may be a better place to start.

Programming is an iterative process by nature, an act of information processing: given a series of inputs and preconditions, produce a series of outputs and post conditions. Information is constantly changing - as those inputs and preconditions change, one's program change to produce correct output and post conditions. The idea that you can know everything there is to know about a program (or on a smaller scale, an object) for the entire life of your program is unreasonable. -- General/MikeTrent

The wave of the future:

*Generative programming: http://www.generative-programming.org/
*Aspect-oriented programming: http://aosd.net/
*What about a paradigm-neutral language? http://portal.acm.org/citation.cfm?id=949350 (ACM subscribers only - sorry)


That describes a language which can be used to do functional, logical, object-oriented, and aspect-oriented programming, all simultaneously.  It's really quite a clever piece of work; it treats types as first-class items, which means that the horror that is C++ template metaprogramming vanishes, and a meta-program looks just like a normal program, with nice properties for inheritance as well.

----

What I have in mind is a way that enables even beginners to develop General/ObjC apps, using the Cocoa framework, in a less manual way.
At least, an interface that would allow coders to overview and edit project items more easily, adding General/RealBasic-like simplicity to tasks like subclassing, and letting you monitor (and also document!!) where each item of your project stands.

At most, it would add a completely new abstraction layer of building blocks. Dialogs (again) that let coders (especially beginners) use forms for coding common tasks, forming blocks of code that are hand-editable, but also remain recognizable by the abstraction layer. It could also be a new set of macros (instead of simplifying the language).

I like the way General/AppleScript is now integrated with Cocoa. Some AS exec once said that the plan is to have AS do everything that Cocoa can. Maybe Apple wants AS to be "programming for the rest of us"? Too bad that there are some inherent performance issues with AS. Or are they not that inherent? Will there be compiled General/AppleScripts that run as fast as General/ObjC code?

Andr�s

----

Seems you want: Better source organisation, better code automation, and more abstraction.  The first one I agree with; General/XCode needs some help in this department.  But "there's a difference between using a screwdriver, and making one from scratch." The last two points are where your suggestions, I believe, fall apart when applied to beginning coders.

C-style languages (including General/ObjC) are very beginner-unfriendly. Automated subclassing is fine, but the user will still have to know the difference between a pointer and a reference, and be able to code with those constructs well; that part of the language can't really be automated in the way you're discussing, and it's perhaps the one most vulnerable to beginner mistakes. I'm not trying to say that such an environment wouldn't be possible, or that it wouldn't be nice, but won't help beginners in quite the way you envision.

----

Then those beginners could focus on these points (i.e. "real" programming issues like understanding pointers). -- Andr�s

----

Try to come up with a dialog that would allow general subclassing in Cocoa. I'd be surprised if you were able to develop anything more than automatically generating class definitions and method stubs, and maybe a simple constructor.  This isn't the part of the code that really hampers beginners. It annoys them, but saving them that time doesn't solve the other problems they have.

----

IB already does some of this. You could also select the methods you'd want to override and Xcode would create stubs for them. It's always an offense when you have to type stuff repeatedly instead of letting the computer do it. This is a bit like the old GUI vs. CLI debate. This would, I think, ease the learning curve somewhat. When they read in a tutorial to "subclass this class and override this method," they would be able to do just that -- without, again, typing a lot.   -- Andr�s

----

The hard part of any project are the bits that can't be generated like that, the one which fall into the "hand-editable blocks of code", which do not tend to be easily abstracted.  I've used General/IDEs like you describe both professionally (Rational Rose before IBM bought it) and for teaching (General/BlueJ - http://www.bluej.org) and while they're useful (Rose obviously more-so) for documentation, organisation, and object design, they do not particularly simplify the actual code that has to be written *which is where beginners have the most problems*.  It's hard enough to find potential nullPointerExceptions in Java at compile time, it's about an order of magnitude harder to catch mistakes using pointers, and no amount of dialogs is going to change that.  Once again, I'm basing this on experience, both using professional-level tools like Rational Rose (before IBM scooped them up) and a beginner-style one for teaching (General/BlueJ - http://www.bluej.org - is this the sort of thing you had in mind?).

----

As long as Cocoa is accessed via C-derivatives, this problem won't go away. But I'm not sure your argument is valid, though: if a solution won't solve the hardest problems, only the easiest ones, it can still be of value IMHO. If I want to compare it to a consumer app like General/GarageBand: using loops, you can do okay music with virtually no talent. Playing the MIDI keyboard, you already need to have some feel for music. But if you want to plug in your guitar and play it, or sing, then you will *really* need your skills, or your music will suck very badly. 

How does that relate to Xcode? Well, playing the guitar is hand-coding. Using musical loops would be the equivalent of graphically adding pre-defined, customizable methods (that Xcode would write based on templates) that do common tasks like saving a document: you could select the instance variables you'd need to save, from a list in a dialog, and Xcode would add the code that does all the archiving. (You could maybe select some advanced preferences, like "Use General/NSDictionary").  Right now, it's being done pretty repetitively through methods with ridiculously long names (for beginners, at least). -- Andr�s

----

To answer your question about General/AppleScript, I expect it will be programming for the rest of us, at least from the way General/AppleScript Studio seems to going.  You'll note that is already lets people do a fair amount of what you're asking for.  As for efficiency, any abstraction layer adds a performance penalty, though in all but exceptional cases the simplicity of the abstraction is worth it (memory is abstracted in Python, for instance, which is less than optimal, but it sure beats the hell out of malloc() for simplicity).  That, by the way, is essentially what General/AppleScript is, an abstraction at a higher level than Cocoa.

----

Are you sure General/AppleScript is just abstraction layer to Cocoa? General/AppleScripts aren't compiled into General/ObjC, are they? An app you write with AS Studio sends itself General/AppleScripts, as you use its GUI (or have it process other events), but anything that's implemented as an General/AppleScript will have an inherent slowness, won't it?

The abstraction I'd love to see in Xcode would be based on macros or graphical representations of code snippet templates that you could customize and insert into General/ObjC code. Once inserted, they'd be General/ObjC code, just as though you'd entered it all manually. There would be no performance penalty.

Btw., I think General/NeXTSTEP was marketed as being "programming for the rest of us," or at least almost so. That is pretty far from being the case even today. -- Andr�s

----

Instead of fighting with Objective C and header files and compilation, just use a simpler, elegant script language like Python. Objective C and Cocoa are great, but the development process is still hard and slow, and you are writing many many lines of code you want to write. For the type of work you describe, you don't need the speed of a compiled language. You will find that Python is much much easier then General/AppleScript, which is easy to read, hard to write. Python is easy to read, write and debug. --General/NirSoffer

----

I have written a (long) article discussing some of my ideas on this matter in General/NextGenerationTools. It was way too long to put it in here. Maybe I can eventually summarize it and put the summary here. Basically I think that the goal is to include more control over higher level decisions, i.e.: design decisions, while writing code, instead of before in some "design phase", and in such a way as it automatically responds to changes in the code. This will necessitate a better way to write source code than just editing interface and implementation files. Hopefully such design tools can be developed iteratively, by extending the capabilities of current tools like General/XCode naturally. -- General/BrentGulanowski

----

I'm not trying to sound negative or anything, but why should this be a part of General/XCode?  General/XCode provides a pretty good editor.  Maybe adding code outline would be neat, and incremental searching and better regular expression searching would be wonderful, but the kind of tool you are describing could sit in a separate application, much like General/InterfaceBuilder is now only for code instead of interface.

I'd rather see effort go into things like separate subprojects, so that I can reuse modules in various General/XCode projects, providing different configurations for different customers or what have you.

-- That would work. But consider this idea: text files are more a convenience to the compiler than to the programmer. Little annoyances like having to manually synchronize the .h file when you change a method signature could be avoided if the IDE allowed more control over the **entities** � classes, categories, protocols, functions, typedefs, enums etc. � that make up a class or source file. It already understands them enough to color them, and to make a popup menu. I'm just saying, extend this with the idea that a class definition is data with more sophisticated structure than a text file captures. A flat text file is a straightforward representation of that data, but it is not the only representation. I think there could be other, more powerful ways of interacting with that data, ways that would allow faster redesign and eliminate some of the tedious work required in manually editing a flat text file representation.

At the same time, I think this could be sympatico with your desire to manage subprojects � or submodules? � between targets *and within them*. � Brent

----

I don't know about the rest of you, but I prefer a text editor over a GUI front end any day. I feel like I have much more control over the code, and I'm also picky about the syntax. Unfortunately, any kind of GUI front end limits your options (some more than others), and sooner or later you will end up having to change something in the code manually. Doing that can be as nightmarish as if someone else wrote the code.

For me, I leave the tedious tasks (such as creating accessor methods and renaming a method) to simple scripts and find/replace, but when it comes to generating all the code for you automatically - I hate that. I say Xcode should stay the way it is - a powerful text editor - and leave the GUI front end to other applications as an option.

In the case of Interface Builder, I think it works because it is not generating the code, it is creating a resource file which makes the program run more efficiently than if it were code. Interface Builder can generate files for custom classes, but this is simply a frame that I end up changing anyway.

Also, just as a side note, I spend much more time thinking of the design of a program then I do typing. I doubt you would see much of a speed increase in programming if a GUI generated the code; however, you would see a decrease in the understanding of the code and how it works - which makes it much more difficult to debug.

-- General/RyanBates

----

I think that I'd spend a lot more time wondering how exactly everything was fitting together if I didn't have simple plain text to figure it out. I don't generally redesign that much after my initial dump.  (If I get in that situation, I'm likely to take advantage of the situation and make really radical changes to the overall design, which would be extremely tedious to set up in a graphical environment!)

Like Ryan, I spend more time thinking than typing, particularly for method signatures. With Objective C, I rarely have to move method signatures between .h and .m files, and if I do, at least I don't even to reformat them (to include "General/MyClass::") like I do with C++.  It really isn't difficult, tedious, or time consuming to Command-C/Command-Option-General/UpArrow/Command-V.

----

"Code generation" was never mentioned nor implied, unless you count changing method signatures. The fundamental observation is that, for all the power of the tools, some of the work is still mechanical and repetitious and maybe it can be automated. We use an IDE because the command line is too sparse in terms of information, and because a full graphical interface means we can navigate faster. Why assume that we are finished innovating?

----

If you spend a lot of time thinking about design, why not have a means to capture that work? Why not make it part of the process and integrate it into the tools? I myself would be happy just to have some little disclosure triangles on method names so I didn't have to scroll all the time and could see my entire class at a glance. The popup is nice but I think it could be improved. But there are other features that interest me, like being able to inspect the entire structure of a project � to see all of the objects graphically, how they connect in terms of references, retains, and messaging, and what variables control changes to the structure. You simply can't get that by staring at text files. �Brent

----

I am open to improvements on using the GUI to enhance the programming experience; however, I see it as a step backwards if the GUI hides the code from the user - which, in turn, gives the user less understanding on how it works. --General/RyanBates

continued at General/XCodeTooMuchLikeATextEditorII
