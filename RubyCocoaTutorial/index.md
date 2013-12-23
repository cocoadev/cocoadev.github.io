---
layout: page
title: RubyCocoaTutorial
---



Alright, so we all love Cocoa. Cocoa is good. However, Apple built Cocoa for one language, and that language doesn't "support" some things it should. Blocks... err, blocks... Well, okay, so mostly blocks and less other stuff. But the more languages the better, right? This tutorial is about RubyCocoa, what it is, how to install it, and how you can make wonderful applications with it. 

First off, RubyCocoa is a nice little bridge between Cocoa and Ruby. From the bridge you can use basically any Obj-C object. Mostly you should stick to Cocoa and other frameworks though, because the POINT of RubyCocoa is writting ruby, not writing more ObjC classes. Ok, so you know what it does. How do you install it now? Well, you've got to dig into the terminal. 

First, log-in to an administrator account. If you don't right now you'll have to undergo some copying & pasting, which I won't help you with.

Open up a new terminal window. Type      cvs -d:pserver:anonymous@cvs.sf.net:/cvsroot/rubycocoa login and hit "enter" when it asks you for a password. (There is no password).

Then type     cvs -z3 -d:pserver:anonymous@cvs.sf.net:/cvsroot/rubycocoa co -P -r branch-devel-panther -d rubycocoa-panther src

For both you **could** copy and paste, but what fun would that be?

then type     cd rubycocoa-panther and     cvs update -d -P

finally, you must configure, setup and install. For the last part of this step you'll need to know the admin password. Type these three commands in:

    ruby install.rb config

    ruby install.rb setup

    sudo ruby install.rb install

Then you are done!!!

OK, so now open up XCode annnnd.... 2 new types of cocoa projects! Alright, so now let's do a Currency Converter project. Create a new RubyCocoa Project and name it Currency Converter (duh). 

Goal 1: Set up the NIB file.

Steps:

1. Open Nib file

2.  Add buttons and fields to window.

3. Set up Controller object and the Converter object.

4. Make all the appropriate connections.

1) Double-Click on Main-Menu.nib in XCode. InterfaceBuilder will start up and open the NIB file.

2) Now I don't expect you to have an extra hour to place the labels and buttons correctly. You can read instructions from the original CurrencyConverter project if you want to have it all perfect, but all you need is:

A label saying "Exchange Rate per $1" and a text field next to it.

A label saying "Dollars to Convert" and a text field next to it.

A label with "Amount in Other Currency" and a text field next to it. 

The default settings should be good for the first 2 text fields, but the 3rd should  have editable de-selected.

Then you need a line, and below that a button with the title "Convert".

All right!

3) Now go to the Classes pane and subclass NSObject twice. One of the subclasses should be named "Controller" and the other "Converter". Now add 4 outlets to controller: converter,
 dollarField, rateField, and totalField. Add an action to Controller and call it convert. Done. Yes, you are done. 

4) Now instantiate Controller and Converter, and connect the outlets in Controller to the proper objects. Connect the button to the Controller and have it call convert:.

Done with goal 1.

Goal 2: Finish up the application with Ruby.

Steps:

1. Add Controller.rb and Converter.rb to your project.

2. Add code to both files to finish off both objects.

3. You are done!

1) Instead of adding the classes to your project via IB, You must add them manually. Close IB and enter XCode. Create 2 new "NSObject" subclasses, Converter and Controller. THEIR NAMES MUST BE THE SAME, CAPS COUNT. Open Controller and insert the following code between "class ....blah blah blah" and "end".

    
include OSX
ib_outlets :converter, :dollarField, :rateField, :totalField

def initialize
end

def convert(sender)
    amt = dollarField.floatValue
    rate = rateField.floatValue
    
    total = converter.convertAmount(amt, rate)
    
    totalField.setFloatValue(total)
    rateField selectText(self)
end

def awakeFromNib
    rateField.window.makeKeyAndOrderFront(self)
    rateField.selectText:self
end


This code should be rather easy to take apart, even if you don't know ruby. (I assume you do, just a little bit.) Now for Converter.rb. Insert this code between class and end:


    
include OSX
def convertAmount(amt, rate)
return (amt * rate)
end



3) You finished! You're done. Finito. Compile-and-Run.
In fact, it should be noticable that this takes a VERY short time to compile. Ruby is a scripting language... so basically it passes the compile time on to the user. Ruby's runtime isn't slow or anything, so unless you're planning to build the next photoshop ENTIRELY in Ruby, it should be fine. (Of course, nothing is wrong with doing a few classes in Ruby)

RubyCocoa has many other uses, explore them and conquer them. Ruby, by itself, is even more useful. -RossLeonardy

