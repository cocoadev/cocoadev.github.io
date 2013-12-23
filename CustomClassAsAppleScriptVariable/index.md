---
layout: page
title: CustomClassAsAppleScriptVariable
---

I'm trying to implement Applescript support in a Cocoa Document-based application, and I've run into an unexpected problem.  Here's the (general) layout:

"MyApp" (an NSApplication) has a ToManyRelationships with the class "MyDocument".

"MyDocument" (subclass of NSDocument) has an attribute named "my stuff", which is a holder for a 

"MyObject" (subclass of NSObject).  "MyObject" has an attribute named "title" (an NSString).  

MyObject also happens to be a holder for other MyObjects.

If I write an Applescript like this:

    
tell application "MyApp"
  set dc to the front document 
  set n to the title of my stuff of dc
  display dialog "the title is " & n
end tell


Things works just fine.  A dialog appears with the correct title of the correct "MyObject".

If, however, I write this

    
tell application "MyApp"
  set dc to the front document
  set mo to my stuff of dc
  set n to the title of mo
  display dialog "the title is " & n
end tell

I get an error - mo is undefined.

Any idea why the second doesn't work, and the first does, would be greatly appreciated.  Especially 

since I'd like folks to be able to run through the items in my container, and it makes for ugly 

scripts when I have to write out the full hierarchy from the document level.

----
Well - I've made some progress, but still not all the way.

I implemented the **objectSpecifier** method for the "MyObject" class.

Now, I can save anything contained by the MyObject object gotten to by the "my stuff" attribute of 

the document in a variable and things work a-ok.  The problem now comes when I try to store

objects underneath that first level.  

In other words, the following hierarchy:

*MyObject (from "my stuff" attribute of MyDocument)

* ob1
* ob2
* ob3

* ob4
* ob5
 
*ob6

 
 the "MyObject" from the my stuff attribute, ob1, ob2, ob3, and ob6 can be stored in a variable,
but ob4 and ob5 cannot.

Here's my code for objectSpecifier.  If anybody's got a suggestion, or needs more information to 

make a suggestion, I'd greatly apprecaite it.  FYI - "MyObject" has a method "parent" which returns 

the "MyObject" (or MyDocument) containing it, and a method "childArray" which returns a mutable 

array containing its children.

    
- (NSScriptObjectSpecifier *)objectSpecifier
{
  id parent = [self parent];
  NSScriptObjectSpecifier *containerRef = [parent objectSpecifier];
  if ([parent isKindOfClass:[MyObject class]]) {
    unsigned index = parent childArray] indexOfObjectIdenticalTo:self];
    if (index != [[NSNotFound) {
      return [[[NSIndexSpecifier allocWithZone:[self zone]] initWithContainerClassDescription:[containerRef keyClassDescription] containerSpecifier:containerRef key:@"childArray" index:index] autorelease];
    } else 
      return nil;
  } else  // "myStuff" attribute
    return [[[NSPropertySpecifier allocWithZone:[self zone]] initWithContainerSpecifier:containerRef key:@"myStuff"] autorelease];
}

