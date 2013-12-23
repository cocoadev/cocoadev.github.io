---
layout: page
title: DWORDEquivalent
---

I'm trying to port some MFC code, and it uses a lot of DWORD data types, and some others such as LPBYTE. Are these Windows specific data types? I know a double word is 4 bytes, so what would be the easiest way to store these in standard C, or Objective-C? Thanks.

Hm ok I'm just huge newb but couldn't I use     int in replacement for     DWORD since they're both 4 bytes?

----

If the actual size is important, then you should use the types is stdint.h, like int32_t. If the size isn't important, then you can just go ahead and use int.  The difference is that int isn't defined to be 4 bytes, it could change in the future.

----

Easiest way IMHO is to pick an equivalent data type and define in Xcode project Other C Flags using the -D[efine] flag
(e.g. -DDWORD=UInt32). This will perform a global replace on DWORD at compile time. - DerekBolli

----

    DWORD  is a double word.      LPBYTE is a long pointer to byte.  Take a look at a reference on HungarianNotation (considered by
many to be a somewhat abominable naming convention), for example http://c2.com/cgi/wiki?HungarianNotation

*Yep I am familiar with this since I am taking an MFC class right now.*

----

So I figured out that DWORD = unsigned int, BYTE = unsigned char, WORD = unsigned short. You can also use this instead of replacing all of them in your code:
    
typedef unsigned char BYTE;
typedef unsigned short WORD;
typedef unsigned long DWORD;
 
typedef BYTE *LPBYTE;
typedef WORD *LPWORD;
typedef DWORD *LPDWORD;


----

  ... or you could just use the actual tokens themselves instead of trying to make Cocoa (Obj-C / Cocoa API) look like MFC code. Down the road, you may thank yourself for becoming 'multilingual' - or being able to easily switch from one language to the next. It'll come in handy when you add yet another language to your repertoire.

