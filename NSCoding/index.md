---
layout: page
title: NSCoding
---

<NSCoding> is a protocol. http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Protocols/NSCoding_Protocol/

Apple's docs on archiving and serialization are here: http://developer.apple.com/documentation/Cocoa/Conceptual/Archiving/

----

If a class conforms to <NSCoding> then it can can be archived. (see UsingArchiversAndUnarchivers)

If a class conforms to <NSCoding> it implements these required methods (i.e. responds to these messages:)

    
-(id)initWithCoder:(NSCoder*)coder;
-(void)encodeWithCoder:(NSCoder*)coder;



What these two methods are expected to do:
  
*  initWithCoder: initialises an instance of the Class from an NSCoder object.
*  encodeWithCoder: writes an existing instance of the Class out to an NSCoder object.


Most of the foundation classes conform to <NSCoding>. For more information on how your class can conform to <NSCoding> see the page on NSCoder.

----

I put together a tutorial on NSKeyedArchiver/NSKeyedUnarchiver and NSCoding:

http://cocoadevcentral.com/articles/000084.php  -- ScottStevenson

