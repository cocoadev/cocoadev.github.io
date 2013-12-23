---
layout: page
title: AppleScriptingCocoaApp
---



See also HowToSupportAppleScript.

I've spent the last few weeks learning how to applescript a cocoa app and thought I would share my learning here.

Mostly this was straightforward and the developer documentation described what was needed.
However there were a few very painful things to learn that I'll out line here.

My toy example was based on the RaiseMan application you can find in the book: Programming Mac OS X with Cocoa. by Aaron Hillegass
it has a simple hierarchy of Application, Document, Person classes with 1 to many's between them.
The document class has an array of persons called employees.

Here's what I learned the hard way:

1. The need to provide a method like
    
- (NSScriptObjectSpecifier *)objectSpecifier

in the Person class.
Without this method defined attempts to access existing person objects would result in 
<class>1 appearing in the event log of applescript.
 
I cut and paste the guts of the method from the Sketch app and its pretty convoluted to figure out whats its doing.
A related point is that the a pointer to the parent document had to be added to Person in order for this 
code to work.

2. The need to provide methods like:
    
- (void)insertInEmployees:(Person *)newEmp atIndex:(unsigned)index
- (void)insertInEmployees:(Person *)newEmp

in the document class
Without these "make new person" wouldn't work.

Note that you also need the LocationRequiredToCreate entry in the scriptsuite file for this form without the position to work.

Mention of these can be found in the documentation for NSScriptKeyValueCoding and especiially in the associated header file in the foundation. As well the 10.2 applescript release notes describe them.

One remaining mystery is that you can't do the following in applescript:
    
"make new person in document 1"

you have to use
    
"tell document 1
	make new person
end tell"

*Actually, you can use     "tell document 1 to make new person", but that's still not as good.*

3. Its more obvious but none the less not documented -  you need a:
    
-(NSString *)description

in the person class for debugging output to get formatted properly.

