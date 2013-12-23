---
layout: page
title: RubyCocoa
---



http://rubycocoa.sourceforge.net/doc/

http://www.rubycocoa.com

Ruby is a dream language to write Cocoa code in -- you get automatic garbage collection *and* an algol syntax, eg:

box = [[NSBox alloc] initWithFrame:NSZeroRect];
[box setEnabled:NO];

would be written in Ruby as:

box = NSBox.alloc.initWithFrame(NSZeroRect)

box.setEnabled(false)

And all of the benefits of Ruby, plus automatic conversion of data types between Foundation and Ruby's core data types (NSDictionary <-> Ruby hash, etc). Both Ruby and Objective-C are derived from SmallTalk, so they're more culturally compatible than (say) Ruby and C++.

[I deleted the old blurb copied from the web site -- go there for a more recent version].

Shall we start a list of applications written with RubyCocoa? I'll plug mine:
RubyCocoa Test Runner A graphical TestRunner for Ruby's Test::Unit. It provides an interface similar to jUnit's for running ruby unit tests. http://rubyforge.org/projects/crtestrunner/ --StevenNewton

----

Why is this a "dream" syntax? How do you translate     [myObject doSomethingWithWeight:a arterySize:b]?

    myObject.doSomethingWithWeight_arterySize(a,b)

or (Ruby has more than one way to do it)

    myObject.doSomethingWithWeight(a, :arterySize, b)

One thing is you don't have to type 'self' so much.  self.myMethod can be written as simply myMethod.

----

At the moment, I agree that Ruby's way is less readable. They're actually working to correct this in Ruby 2, and the proposed syntax that I've heard is actually clearer than ObjC's:     myLad.kiss(kissee:girl, withLipstick:false), with the arguments able to go in any order as long as the argument's id is included.

*Hey! Ruby 2 stole my syntax! I was (and still am) planning to use that basic idea for method calls in my programming language, as soon as I get around to writing it. :) --JediKnil*

Yes, because dot notation is quite unique to you and Ruby. Damn those thieves! ;-)

*dot notation sux0rs*

Umm, no, I meant passing named arguments. Which, as I discovered, also exists elsewhere, but not in many OO languages. --JediKnil

----

To a limited extent, named arguments exist in C# and VB (both classic and .NET, IIRC.)

