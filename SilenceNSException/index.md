---
layout: page
title: SilenceNSException
---

I'm writing some unit tests, and I'm testing some conditions in which an exception is supposed to be thrown.  So I've come up with this macro:

#define DDAsertException(condition, errorMsg) \
do{ \
  BOOL threwException = NO; \
  @try { condition; } \
  @catch (id e) { threwException = YES; } \
  @finally { NSAssert(threwException, errorMsg); } \
}while(0)

An example of its usage (doing something that is supposed to throw an exception):
DDAssertException([ddElement addAttribute:nil], @"Failed test 9");

This works well and good, but I still see the exception being printed on the console:
*** Assertion failure in -[DDXMLElement addAttribute:], ...

It doesn't crash the app or cause any problems what-so-ever.  But it still prints the exception on the console. This always catches my attention, and makes me think something went wrong.  Is there anyway I can do to prevent the exception from being printed to the console?

For those thinking, "why would you want to..." - this is for the [http://code.google.com/p/kissxml/ KissXML] project.  The purpose of this project is to act exactly like NSXML.  So if NSXML throws an exception, so should KissXML.

