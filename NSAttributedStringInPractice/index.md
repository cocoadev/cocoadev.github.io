---
layout: page
title: NSAttributedStringInPractice
---

Firstly my apologies for giving in and posting here on what I am very sure will appear a very trivial concept to many here.

Although useful information regarding NSAttributedString is already available, both here and in the developer documentation I am still having trouble using the class despite fervent Googling.

I am attempting to create an NSAttributedString from a plain string I already have, but want it to be in the system font but just a little smaller, say font size 7. Here is what I have already, but it doesn't work. If anyone has a practical example of using NSAttributedString it would be of great value. 

<source lang="objc">
NSFont *stringFont = [NSFont fontWithName:@"Arial" size:7.0];
NSDictionary *stringAttributes = [NSDictionary dictionaryWithObject:stringFont forKey:NSFontAttributeName];
NSAttributedString *lowerString = NSAttributedString alloc] 
							initWithString:stringFromElsewhere
						        attributes:stringAttributes];
</source>

Again, many thanks to all.

----

here's another practical example to set font, color and size:

<source lang="objc">
    NSDictionary *attributes = [[NSDictionary alloc] initWithObjectsAndKeys:
                        [NSFont fontWithName:@"Helvetica" size:26], NSFontAttributeName,
                        [NSColor whiteColor], NSForegroundColorAttributeName, nil];
    NSAttributedString *myString = [[NSAttributedString alloc] 
							initWithString:stringFromElsewhere
						        attributes: attributes];

</source>

[[EcumeDesJours

----

Attributed strings can be tricky at first, but it looks like you are doing everything correctly. How is the string being displayed? Perhaps the error is in that code.

If you want the system font, you may want to use the class method <source lang="objc">[NSFont systemFontOfSize:7.0]</source>

Also, don't forget to release the attributed string once you're done with it. It's easy to forget that.

-- RyanBates

----

In particular, don't use something like <source lang="objc">[textView setString: lowerString]</source> which only takes a non-attributed NSString. Do <source lang="objc">textView textStorage] setAttributedString: lowerString]</source>

----

As for your code, I think your problem is in the first line; you need to pass the font's Postscript name to     +fontWithName:size: not it's display name.  You may find the [[NSFontManager function     -fontWithFamily:traits:weight:size: easier to use for manually specify fonts as the family is usually what you think of as the name.  For example, your first line would become     NSFont *stringFont = [[NSFontManager sharedFontManager] fontWithFamily:@"Arial" traits:0 weight:5 size:7.0]; -- Bo

*I don't think that's the problem - I pasted the identical code into a test project and it worked fine when the text was output with     insertText:*

----

Thankyou for your input, the problem was of course that I was using setString which happily ignored my Attributions. That said, the process of using NSAttributedString seems very verbose.

----

I would like to load an NSImage into an NSAttributedString.  I have tried to use the FileWrapper, but this doesn't seem to work:

    
NSFileWrapper *wrapper = [[NSFileWrapper alloc] initWithSerializedRepresentation:[myImage TIFFRepresentation]]; // returns nil


I realize that I could write the NSImage out to a temporary file and then call [[NSFileWrapper alloc] initWithPath:tempPath].  But is there a way of placing an NSImage into a NSAttributedString more elegantly (without file I/O)?

Thanks, Joe

----

I found a solution out there in the Macosx-dev archives:  (http://www.omnigroup.com/mailman/archive/macosx-dev/2002-March/025069.html).

Here is my code, in hopes that it might be usefule to someone:

    
NSTextAttachment   *attachment  = [[NSTextAttachment alloc] init];
NSCell             *cell        = [attachment attachmentCell];
[cell setImage:myImage];
NSAttributedString *imageString = [NSAttributedString attributedStringWithAttachment:attachment];
[attachment release];


-Joe

----

Thanks Joe, yes i was asking myself how to achieve this :)

----

Just to save you the trouble to those of you who were wondering, NSAttributedStrings cannot be put into NSTextFields. The solution to this problem (or what I did), was resize the TextView to look like a TextField.

----

Sure it can. Try     [myTextField setAttributedStringValue:someAttributedString];. This method is documented in NSControl, a superclass of NSTextField.

----
You're right, you can put an attributed string in NSTextField<nowiki/>s. Problem is, the attributes get lost as soon as the user starts editing and stay lost when they're done. I think you can pass the attributed string to the fieldEditor when editing starts and get it out of the fieldEditor when editing is finished to pass that back to the text field...real nice.

----
Well, check out NSTextField's     setAllowsEditingTextAttributes:, NSControl's formatting methods, or NSFormatter's     attributedStringForObjectValue:withDefaultAttributes:. At least *one* of those might be able to help you out.

