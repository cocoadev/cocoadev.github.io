---
layout: page
title: NSDictionary
---

Documentation from the AppleDeveloperConnection:

https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSDictionary_Class/Reference/Reference.html

See FoundationCollections for a general overview; see DesignDictionary for a critique of the dictionary design in general.

----**Notes:**

Here's a little example I threw together to use NSDictionary with some strings.  It's not particularly good, but it might help you understand a little better.
    
 #import <Cocoa/Cocoa.h>
 
 int main(int argc, const char *argv[])
 {
   NSDictionary *dict;
   NSEnumerator *enumerator;
   id key;
   NSAutoreleasePool * pool = NSAutoreleasePool alloc] init];
   
   dict = [NSDictionary dictionaryWithObjectsAndKeys:
           @"A beverage prepared by heating cocoa with water/milk",
           @"cocoa",
           @"http://www.cocoadev.com/",
           @"cocoadev",
           @"4 : a list (as of items of data or words) stored in a \
           computer for reference (as for information retrieval \
           or word processing)",
           @"dictionary",
           nil];
   
   // printf("entry for cocoa: %s\n\n",
   //        [[dict objectForKey:@"cocoa"]cString]);
   // Use of the cString method is discouraged ASCII is out Unicode is in.
   // Also watch out for [[dict objectForKey:@"cocoa"]cString] notice the
   // lack of a space between the receiver and the message.
   // And lastly since this is a Cocoa example why not use an [[NSLog()
   NSLog(@"entry for cocoa: %@", [dict objectForKey:@"cocoa"]);
   
   enumerator = [dict keyEnumerator];
   
   while ((key = [enumerator nextObject])) {
     //printf("%s : %s\n", [key cString],
     //        dict objectForKey: key] cString]);
     NSLog(@"%@ : %@", key, [dict objectForKey:key]);
   }
   
   [pool release];
   
   return 0;//[[NSApplicationMain(argc, argv);
 }


----
I switched the order of "dictionary" and its definition. It looked like it was in key-object order instead of object key. -- GKinnel

