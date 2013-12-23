---
layout: page
title: NSDecimalNumberBehaviors
---

**Protocol Description**

The NSDecimalNumberBehaviors protocol declares three methods that control the discretionary aspects of working with NSDecimalNumber(s). The     scale and     roundingMode methods determine the precision of NSDecimalNumber�s return values and the way in which those values should be rounded to fit that precision. The     exceptionDuringOperation:error:leftOperand:rightOperand: method determines the way in which an NSDecimalNumber should handle different calculation errors.


For an example of a class that adopts the NSDecimalNumberBehaviors protocol, see the specification for NSDecimalNumberHandler.
 ----
**Apple Documentation**

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Protocols/NSDecimalNumberBehaviors_Protocol/

----
**See Also**

*NSDecimalNumber
*NSDecimalNumberHandler

