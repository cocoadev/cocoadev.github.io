---
layout: page
title: NSFontForTableView
---

I am trying to change the Font to Bold or a Color for a NSString instance within the code for an object in a tableView. This is a document based app. The object class has the basic setter and getter methods. When I introduce a condition into the method I want to change the Font to Bold or a color. eg.

    

(NSString *)myString
{ 
    return myString;
}

(void)setMyString:(NSString)aString
{
     [aString retain];
     [myString release];
      myString = aString;
}

//Aaron Hillegass's book Cocoa Programming for OSX pg. 274 gives this code:

//fontManager = [NSFontManager sharedFontManager];
//boldFont =  [fontManger convertFont:aFont toHaveTrait:NSBoldFontMask];

//I'm not sure where or how to use this in my method. 
//I think the new code for the condition might be something like:

(void) setMyString:(NSString)aString
{
    if ( whatever condition){
         fontManager = [NSFontManager sharedFontManager];
          boldFont =  [fontManger convertFont:aFont toHaveTrait:NSBoldFontMask];
     [aString retain];
     [myString release];
      myString = aString;
    [myString initWithBoldFont];//or[set myString:(NSFont *)boldFont];
    }else{
   [aString retain];
     [myString release];
      myString = aString;
}
}

I know this isn't right, as it won't compile. Does anybody know how to do this? Thanks!!!
  DSG

----

are you looking for something called NSAttributedString (in which you can set attributes like font, size, and color?) Aaron's book eventually gets into this subject, unless my memory fails me. There is also a mutable version of this class, in case you need it.

----
I looked at NSAttributedString but, from what I can tell, you have to use     drawAtPoint in an NSView with it. I also looked at some sample code for a To Do app and they developed a separate NSValueTransformer class for changing the color of the tableview cell. There should be a way to change or override the font for the instance variable inside the class somehow. Any help woud be appreciated Thanks!!!

----

You can use NSAttributedString in lots and lots of ways. You don't really want to mess around with subclassing NSString at your level of expertise (even though you may have oodles of experience with another object-oriented API). Cocoa has a bucketload of classes for dealing with strings, some of them are arranged in complex and beautiful class hierarchies.

The use of strings with attributes, in NSTextView, for example, is an often-discussed topic. The NSTextStorage instance of NSTextView inherits from NSMutableAttributedString. You write: *There should be a way to change or override the font for the instance variable inside the class somehow.* What class? Do you mean NSString? Do you mean the NSCell instance? Do you mean the model class you are using to contain the data for your table? If you are looking at Hillegass's "Big Letter View" example, yes, he is drawing an attributed string into a custom view. That is most certainly not the only thing you can do with an attributed string.

Please consult the documentation for NSCell, particularly the     setAttributedStringValue: method, which is what I used to write an attributed string into a text field cell. I confess my experience with NSValueTransformer is negligible, so if you're pursuing an example that uses it, I am afraid I can't be of much help.

Of course the code in your     setMyString: method will not compile. There **is no method**     initWithBoldFont anywhere in the Cocoa frameworks, let alone on NSString (although you could always
**write one yourself** if you were subclassing a string class with attributes, but see again my advice about subclassing NSString). Perhaps this is a Java or M-F-Cee-ish parlance.
The alternate syntax     [ set myString: (NSFont *) boldFont ], while creative, would have absolutely no meaning in ObjectiveC, the language in which you appear to be writing your code. 
You cannot **send an object as a message to a method.**
You use a method by invoking it **as a message to an object** (in the square bracket formalism). You cannot use an NSFont object as an argument to a method that expects the argument to be an NSString. And it would be completely bizarre to send an     initWithWhatever message (assuming it was a valid message) to myString after assigning it the object pointed to by aString in your setter method. You would end up in a world of hurt. You can't make this stuff up as you go along.

What you really need to do is to sit down and absorb the material in the early chapters of Hillegass's book,where he provides a primer on ObjectiveC as a language, and outlines the architecture of the Cocoa frameworks. Sorry to mix metaphors here, but you appear to have dived into this half-baked. There are numerous pages here at CocoaDev for beginners. Check out CocoaIntroduction or GettingStarted.

CanNotRunMyAppOnOtherMacs presents an even-more-roundabout path arriving at this bit of advice.

