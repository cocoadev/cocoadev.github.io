---
layout: page
title: StringByAppendingStringQuestion
---

What is wrong with the following code:
    
General/NSString *aString = @"test";

[aString stringByAppendingString: General/sender cell] title];
[[NSLog(@"title = %@", General/sender cell] title]);
[[NSLog(@"aString = %@", aString];



The run log shows that "title" = the title of the button pressed and that "aString" = "test".
Why isn't the title getting appended?

----

stringByAppendingString **creates and returns** a new General/NSString as you want it. The code would work if you wrote:

    
General/NSString *aString = @"test";

General/NSString* anotherString = [aString stringByAppendingString: General/sender cell] title];
[[NSLog(@"title = %@", General/sender cell] title]);
[[NSLog(@"aString = %@", anotherString];



Note that the new string is autoreleased.

----

**Discussion of alternatives to     stringByAppendingString:**

How do I get a certain value that a user typed on a text field and then add it to another text field?
For example: I have a button called "Create Sentence" and two text fields, yourName and theResult. When the user presses the Create Sentence Button, it gets the string value from yourName and then returns the text "Your name is <yourname General/StringValue here>" into theResult field.

The problem is that I don't know how to tell it to set the String Value of theResult to "Your name is " PLUS yourName's string Value.
In most languages I know (Java, General/JavaScript, General/ActionScript, HTML, General/RealBasic) you do that by using "&", "&&" or "+", but Cocoa won't accept those with strings.

---- 

Cocoa is based on Objective-C which is a strict super set of the C language.  To obtain the behavior you want you are going to make use of General/NSString's 

        General/[NSString stringWithFormat:@"Hello %@, welcome to my Application!", [nameField objectValue]]

The formatters in the first string are replaced by the values in the comma separated list following the format string.  The formatters can be found elsewhere on this sight under topics on General/NSLog() and other functions that use the same fomat.  This is how strings are appended (in my experience) with Cocoa.  For a quick list of tokens, you've got %d -> decimal number, %f -> float, %@ -> an object, etc...

----

You could also use:

    [@"Hello " stringByAppendingString:[nameField stringValue]]

----

You could also use:

    [ [ General/NSArray arrayWithObjects:@"Hello ", [nameField stringValue], @" welcome to my application", nil ] componentsJoinedByString:@"" ];

String concatenation is a very fundamental thing, for almost any programming task, and most languages/General/APIs have a thorough, if sometimes over-engineered string abstraction, and Cocoa is no exception. The docs are easy to find ( /Developer/Documentation/Cocoa ), but if you don't look, or don't know where to look, you'll always have to ask little questions, and you'll never be able to focus on the Big Idea. 

In the beginning, this kind of stuff may seem daunting ( believe me, I came from Qt programming under linux in c++ ) -- we're always ready to help you, but you need to learn how to help yourself, too ;)

I recommend googling for the General/CocoaBrowser (it's an excellent front-end to the documentation that comes with the developer tools ).

--General/ShamylZakariya

----

As a note, stringByAppendingString: is probably the fastest way to do this. stringWithFormat: has to scan the string looking for '%' tokens. With arrayWithObjects:, you have the overhead of creating an object (an then adding it to the autorelease pool), then componentsJoinedByString: has to loop through the array an (presumably) append each string to a mutable string. Each time a new string is encountered, the mutable string has to realloc memory as when it was created it would have no idea about how much memory to store. It's also the ugliest method for something like this, but is of course enourmously useful other times. I realize it was only shown for demonstration purposes.

----

Just to clarify what's going on in the     [@"Hello " stringByAppendingString:[nameField stringValue]] example above, the @"Hello" is an General/ObjectiveC convenience that creates an General/NSString object that contains the string "Hello" and sends a stringByAppendingString message to it. Otherwise you'd be stuck using General/[NSString stringWithCstring] or stringWithFormat all the time to create strings. I know when I was a newbie it took me awhile to understand that the @"" notation could be used this way.

----

I wouldn't call it so much a "convenience" but a compiler aide. It is actually a truly constant string. That is, if Foo.m uses the string @"foo" and let's say General/FunkyFod.m uses @"foo", both @"foo" strings point to the exact same object.

----

**Example with URL strings**

How do I arrange it so openURL opens a URL using a default value plus a user defined value?
For example: 

General/[[NSWorkspace sharedWorkspace] openURL:[NSURL General/URLWithString:[userInput stringValue]]];

In "userInput stringValue" I want to have part of an URL (like http://www.mywebsite.com/) and, added to that, the user input (like his name) so the web browser would open http://www.mywebsite.com/John .

----

You should do [myURLString stringByAppendingString:[userInput stringValue]] instead of just [userInput stringValue].

----

This is another pretty reasonable way to do it:

General/[[NSWorkspace sharedWorkspace] openURL:[NSURL General/URLWithString:General/[NSString stringWithFormat:@"%@%@", @"http://www.mywebsite.com/", [userInput stringValue]]]];

- General/GusMueller

----
Be careful with user input and anything related the HTML. First of all, you need to URL-ize it by replacing spaces with "%20", among other things. Next, you are allowing the user to specify their own URL. In a simple example like this, it is pretty harmless. But for complicated sites, there are many examples of Javascript server exploits. Look at the error log of any Linux-based Apache server on the Internet to see the kinds of General/URLs the script kiddies are going to throw at you.
----
