---
layout: page
title: JavaScript
---



A scripting language used on web pages, and originally created by Netscape. JavaScript offers basic interaction, but no file system access. If you're interested in adding JavaScript capabilities to your applications, the Mozilla JavaScript engine is open-source - get it from http://www.mozilla.org/

Also check out Rhino from the same site ( http://www.mozilla.org/rhino ) which is a JavaScript implementation in Java, with a command line interface. It's bundled with JSC which is the JavaScript to Java byte-code compiler (do I have to point out how wildly cool this is?).

--TheoHultberg

----

JavaScript for OSA adds the possibility of writing AppleScript in JavaScript (with the mozilla engine!), look for it here: http://www.latenightsw.com 

--TheoHultberg

----

JavaScript is an interesting language to program in; it is not object oriented in the same way as ObjC, C++ and Java is, where every class inherits form other classes in a tree-like structure. In JavaScript inheritance is created by something called prototypes. 

There are no real classes in JavaScript everything is objects, and new objects are made by calling a function that acts as a constructor (init in ObjC), the function itself is an object. Instance variables can be added on the fly, wherever, whenever, some examples:

    

// MyClass is a constructor function for MyClass, not a real class,
// but as close as one gets
MyClass = function( name ) {
   // an instance variable
   this.name = name;

   // a method
   this.sayHello = function( ) { alert( "Hello my name is " + this.name ); }
}

// let's try, so long everything looks like ObjC/Java...
var myObject = new MyClass( "Theo" );

// shows the value of the instance variable called name
alert( myObject.name );

// alerts "Hello my name is Theo"
myObject.sayHello();

// let's do some unconventional (not-like-ObjC-C++-Java) things:

// add a new instance variable to myObject:
myObject.surname = "Hultberg";

// another way of doing the same thing, showing that JavaScript 
// objects really are dictionaries/associative arrays
myObject["surname"] = "Hultberg";

// this is cool: add a new method to the object:
myObject.saySurname = function( ) { alert( this.surname ); }

// let's do some very odd and cool things, showing the power 
// of prototypes. so long we have just modified the actual object
// now we will modify all objects created with MyClass:

// add an instance variable
MyClass.prototype.className = "MyClass";

// add a method to all objects of this type
MyClass.prototype.toString = function( ) { return "[object MyClass]"; }

// note that these are NOT class methods, but new instance methods
// so when I do this, I get "[object MyClass]"
alert( myObject.toString() );




JavaScript is really the coolest scripting language there is, I'd love to see a CLI version of it, with access to the filesystem and GUI-routines. Most people just thinks it's a C-style scripting language for the web, but it's not. 

There is actually a CLI version in Rhino, see above. [late edit]

-- TheoHultberg


RUMOR has it that Mac OS X 10.2 will be scriptable not just by AppleScript, but also via JavaScript !!!  Your CLI wish just may come true sometime late this summer.  :)

Bummer that it didn't (current version is now 10.2.2 and no JavaScript)...


Well, as posted above, it already is, just not made-by-apple (see www.latenightsw.com). It would be nicer if apple did it, since then it would be more integrated, now one needs JavaScript for OSA. AppleScript is far from the CLI, though. It would be cool to be able to make JavaScript-Cocoa-apps in AppleScriptStudio though. 

--TheoHultberg

----
Apple's recent contribution to the KDE kjs project (http://www.opensource.apple.com/projects/others/JavaScriptCore-0.2.tar.gz) is a nice sign.

----

Brendan Eich, the creator, talks about his creation and where it's heading: http://weblogs.mozillazine.org/roadmap/archives/008325.html

-- Theo

----

Apple's JavaScript implementation includes a kind of bridge (similar in spirit to Cocoa-Java, AppleScript Studio, PyObjC et al.) between Objective-C objects and JavaScript. It can make Objective-C objects behave like JavaScript objects when used by JS code, and allows access to JS objects from the Objective-C side by using proxies and setValue:forKey: / valueForKey:. However, I don't know if it can handle some features that are usually essential for code to be completely useable to create a full Cocoa app (i.e. subclassing), although I'm surprised Apple didn't create an AppleScript Studio-like environment in Xcode for JS. See the plugin architecture for Dashboard for a demonstration of what JSCore can do on the Objective-C side. -- l0ne aka EmanueleVulcano

