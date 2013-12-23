---
layout: page
title: MySQLandCocoa
---

I was wondering whether anyone could tell me the easiest way to interface MySQL through cocoa. 

I found this: http://mysql-cocoa.sourceforge.net/ 

But it looks old and somewhat dated... I don't really know. Anyways, what are the masses using these days?

- JohnDevor

----

I used it at work to create a small app that needed mysql access. It works fine. The only problem was that I needed to loop 7000 times over a SELECT query and investigating the row data each time. The framework returns an autoreleased dictionary or array so memory usage tends to get high. I solved it by alloc/initing and retaining it in the framework. Once you know this you can actually release it yourself (i.o. having it put in the autorelease pool). It's not the best way, but it reduced memory usage from 500 MB to approx 100 MB while running. -- KasPer

*I wonder if making your own autorelease pool for the loop would let you cut down on memory usage without having to modify the framework. - AdamVandenberg*

----

Hi;

I'm the author/maintaner of mysql-cocoa; I realise that it has not been updated for a while, but it is now, and I also try to keep it working at all time. I'd be happy to answer any of your question about the framework I'm developping, and if you have some feature request usualy I take them into account.

The main raison for which the framework was not changed for a while was that no-body complained about it nor asked for new features, which one can interpret optimisticaly as a good sign of stability/completness... still I'd be a bit more cautious and I have to admit the project was a bit complicated to use; that why I just released a new version which is (hopefuly) easier to use (you don't have to compile anything yoiur self anymore, you can get it as a binary ready to be linked with).

Serge (my address is on the site).

----

60 Kbyte for each row? That seriously limits the applicability of the framework, not to mention its speed.
I would think that if you only have 7000 objects, you should not be using any database.

stephan-at-stack.nl

----

Has anyone been able to get the mysql-cocoa framework to work with XCode on 10.3?  I am not too familiar with 3rd party frameworks, but I have not been able to get any of the examples to compile, they all give weird errors about not being able to find the code, or it is all mis-named, I even tried to get the source for CocoaMySQL (http://cocoamysql.sourceforge.net/index.html), which is a great program, but the source for it will not compile for me either.  Any suggestions would be appreciated, I am trying to write an input program so I can easily add data to my blog.  Thanks
     --DerekCramer

----

The C ODBC API can be accessed via *sql.h*, *sqlext.h*, and *sqltypes.h* in */usr/include*.

One of the better guides for this API is the *ODBC Programmers Reference* on the MSDN.

http://msdn.microsoft.com/library/default.asp?url=/library/en-us/odbc/htm/odbcabout_this_manual.asp

----

I have added the "sql.h" header file, but I get a conflict between the declaration of BOOL in "sqltypes.h" and "objc.h"
sqltypes.h declares BOOL as int while objc.h declares it as char. Anyone come across this?

----
Yes, the header is kind of broken in the Objective-C/C++ context.

I currently bring in the ODBC headers like the following. It hasn't burned me yet, and it prevents sqltypes.h from defining BOOL. No part of the API uses the BOOL definition, so it should be safe.

    
#define BOOL BOOL
#include <sqlext.h>


----

Is there any documentation for mysql-cocoa? I can't tell what the different frameworks are even for.

Also, I really want to use MySQL with Cocoa bindings. No matter how much I search all I can find is people asking basic questions or somehow implying that they are actually doing that. I've yet to see any clear answers on how to accomplish it. Do I need to subclass the Cocoa provided controller classes?

Thanks
----
Unbelievable how few have actually published about developing database applications with Cocoa. If only Apple would re-do the DBKit for XCode 3.1 with all the new stuff, then I think a lot of iMacs could be nice front-ends to corporate databases. A lot of stuff now built in PHP could benefit from the nice Cocoa UI. Steve Jobs promoted databases in the early nineties, so it might not be cool enough now for him, but a platform without proper database support will never catch on with developers, and SQLite is simply not good enough.

----

The binary is PPC only and there are a lot of errors popping up while building under Leopard. Will MCPKit be updated or is the project dead?

