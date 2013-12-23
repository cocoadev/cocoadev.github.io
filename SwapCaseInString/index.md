---
layout: page
title: SwapCaseInString
---

I wish to change uppercase letters to lowercase and vice versa.

Currently I have this code in use:

    

template <typename _InputIter, typename _OutputIter>
void swapCase (_InputIter first, _InputIter last, _OutputIter res)
{
   General/NSCharacterSet *lowercase = General/[NSCharacterSet lowercaseLetterCharacterSet];
   General/NSCharacterSet *uppercase = General/[NSCharacterSet uppercaseLetterCharacterSet];

   for(; first != last; ++first)
   {
      unichar ch = *first;

      General/NSString *str = nil;
      if([lowercase characterIsMember:ch] == YES)
         str = General/[[NSString stringWithCharacters:&ch length:1] uppercaseString];
      else if([uppercase characterIsMember:ch] == YES)
         str = General/[[NSString stringWithCharacters:&ch length:1] lowercaseString];

      if(str != nil)
      {
         std::vector<unichar> v([str length]);
         [str getCharacters:&v[0]];
         res = std::copy(v.begin(), v.end(), res);
      }
      else
      {
         *res++ = *first;
      }
   }
}



I realise that it can be made faster by either reusing the General/NSString object or do explicit alloc/release (rather than rely on the implicit autorelease -- though autorelease would still be the case for lowercaseString or uppercaseString, so at best it would only be twice as fast).

Is there another way to change the case of a unichar?

And for the records, accumulating all lowercase letters in one string and uppercase letters in another won't work, since e.g. the lowercase version of '�' is 'ss' (according to Apple, not according to my German friend :-) ).

----

Responding to myself: To avoid all the temporary strings accumulating in the auto release pool there is the option of using Core Foundation string services, although basically they do the same (i.e. create string object with character, make a mutable copy, lowercase that copy, extract characters and dispose the two objects).  I guess another workaround is to set up a local auto release pool which get flushed on every iteration.
