---
layout: page
title: NSHexNumberFormatter
---

I am writing a debugging application, and want to display numbers in an appropriate hex format (32 or 64-bits) in an NSTextField.

However, the NSNumberFormatter is missing the hex option!  

What is the best way to achieve this result?

At the moment, I have a special accessor for each value, e.g.:

-(NSNumber*)value
{
  return value;
}

-(NSString*)valueAsHex
{
  return [ NSString stringWithFormat:@"0x%08x", [ NSNumber unsignedIntValue ] ];
}

but this feels wrong, since it essentially modifies the *model* to get a UI result.

----

You are free to derive your own NSFormatter(s).

