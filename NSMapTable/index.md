---
layout: page
title: NSMapTable
---

http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/NSMapTable_class/Reference/NSMapTable.html

A flexible alternative to NSDictionary.  As a tradeoff, it's a bit more of a pain to use. 

On the other hand, I don't think this buys you anything over CFDictionary, and CFDictionary may be more convenient since it's bridged to NSDictionary.

----

There's a bug in NSMapInsertIfAbsent as of 10.3.7.  According to the docs (1/16/2005),

    
 void *NSMapInsertIfAbsent(NSMapTable *table, const void *key, const void *value)
 
 If key matches a key already in table, NSMapInsertIfAbsent returns the
 preexisting key; otherwise, it adds key and value to the table and
 returns NULL. Raises NSInvalidArgumentException if key is equal to the
 notAKeyMarker field of the table's NSMapTableKeyCallBacks structure.


The actual behaviour is that it returns the preexisting **value** in the situation where it claims to return the preexisting key.  Try this sample code:

    
 #import <Foundation/Foundation.h>
 
 int main (int argc, const char * argv[]) {
     NSAutoreleasePool * pool = NSAutoreleasePool alloc] init];
 
     NSMapTable *map = NSCreateMapTable(NSObjectMapKeyCallBacks, 
                                        NSObjectMapValueCallBacks, 
                                        0);
 
     NSString *preexistingKey = @"key";
     NSString *preexistingValue = @"preexistingValue";
     NSString *newKey = [[preexistingKey mutableCopy] autorelease];
     NSString *newValue = @"newValue";
 
     // new key is equivalent to preexisting key, but not == equal
     NSCAssert(preexistingKey != newKey, nil);
     NSCAssert([preexistingKey isEqualTo:newKey], nil);
 
     NSMapInsert(map, preexistingKey, preexistingValue);
             
     id result = NSMapInsertIfAbsent(map, newKey, newValue);
 
     if (result == preexistingKey)
         NSLog(@"NSMapInsertIfAbsent returned the preexistingKey.");
     else if (result == preexistingValue)
         NSLog(@"NSMapInsertIfAbsent returned the preexistingValue.");
     else
         NSLog(@"NSMapInsertIfAbsent returned something else.");
     
     NSFreeMapTable(map);
         
     [pool release];
     return 0;
 }


It will print out "NSMapInsertIfAbsent returned the preexistingValue." when it should print out "NSMapInsertIfAbsent returned the preexistingKey."

Be careful, this tripped me up!

(submitted to apple, if you couldn't tell)


----

*
A flexible alternative to [[NSDictionary?. As a tradeoff, it's a bit more of a pain to use.
*

What are the real advantages of NSMapTable ? speed ? does anyone has a benchmark that show if it is faster than an NSDictionary ?
another question, in the NSDictionary doc, they say that internally, the NSDictionary uses NSHashTables !? AFAIU, it is probably an NSMapTable ?
thanks

*NSDictionary forces certain behaviours, such as copying its keys and retaining its values. NSMapTable and CFDictionary allow you to customize what happens when keys and values are inserted, so you can have more control. This would be useful to, for example, store pointers to non-objects as the keys, or make a dictionary that didn't require its keys to be copyable.*

