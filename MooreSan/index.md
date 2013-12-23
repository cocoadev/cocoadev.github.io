---
layout: page
title: MooreSan
---

MooreSan 

I like the EiffelProgrammingLanguage for ProgrammingByContract and clean MultipleInheritance.  I like AppleScript for ConversationalProgramming.  I like JavaScript for runtime fun with closures, everything being an object (including functions) and runtime overridable inheritance ^_^.  I like CPlusPlus for introducing me to templates (STLGoodness).  I like LangPerl because it's like awk but better!  I like LangBasic because I started programming on an AppleII (and QBasic for F5.. did I mention I made use of mouse, images, and sound in my QBasic programs...).  I like Cocoa, because of the DevelopmentEnvironment (XCode, InterfaceBuilder) and the clean division between OOCode and C.  (At first I thought it was silly to have the [reciever message] syntax as being inconsistant with the C underpinnings.  Now I think that because it's seperate it makes it very clear when you are working on an object, versus touching member variables etc.)

I'm a Canadian in Japan, teaching English in Elementary Schools.  I have a Bachelors of Biblical Counseling.  I write programs for making programs (ScriptingLanguages, MacroLanguages, etc.), programs for making teaching resources (handouts, gameboards, card sets, etc.), and programs for recreation (tetris AIs, UnixLikeOperatingSystems in ECMAScript for portability between flash and web browsers etc.)

----

Some things I am using/working on in my current projects: InvarianceCheckingWithAspectCocoa, ACCurrentMethod (now considered uneccesary), PluginArchitecture, WebKit, DragAndDropTables, NSTextViewAsWiki, FSObject, DOScriptingArchitecture (see DOStrategy).

there's more but I'll add them later.

----

Current Cocoa design pattern thoughts:

Model Manager (Vender) Controller View.  I only code the middle three.  Model is completely Foundation classes (NSUserDefaults/NSDictionary/NSString... sometimes I add categories to them so that relevant code stays on that layer). Manager only talks to the Model and contains all the application logic for manipulating the data (if it's of a more general nature I put it in the affore mentioned category on the class itself). Vender is an object implementing a DOScriptingArchitecture protocol and registers itself on launch on a port with the bundle id of the application (usually I vend methods that give access to data without manipulating it).  Controller is a delegate/datasource/target for AppKit classes, and that's all... (it has a reference to a Manager object which it asks about data state, or tells to manipulate the data... but the controller really only responds to and sends messages to ApplicationKit and other NSView sub-classes).  View is AppKit.

I like NSUserDefaults; I don't like TooMuchXML...I like CoProcessing; I don't like threads.... I like categories and ObjC's orthogonality to C; I don't like CPlusPlus or Java because they're not ObjC (no categories, not orthogonal)... I like UNIX; I don't like Microsoft... I like FScript and JavaScript; I don't like macros...  I like DistributedObjects; I don't like SQL...I like Interface Builder; I wish IB did more... I like cheese...

