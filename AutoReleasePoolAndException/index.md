---
layout: page
title: AutoReleasePoolAndException
---

<code>Released autorelease pools, if not on the top of the stack, cause all (unreleased) autorelease pools above them on the stack to be released, along with all their objects. If you neglect to send release to an autorelease pool when you are finished with it (something not recommended), it is released when one of the autorelease pools in which it nests is released.


This behavior has implications for exceptional conditions. If an exception occurs, and the thread suddenly transfers out of the current context, the pool associated with that context is released. However, if that pool is not the top pool on the thread�s stack, all the pools above the released pool are also released (releasing all their objects in the process). The top autorelease pool on the thread�s stack then becomes the pool previously underneath the released pool associated with the exceptional condition. Because of this behavior, exception handlers do not need to release objects that were sent autorelease. Neither is it necessary or even desirable for an exception handler to send release to its autorelease pool, unless the handler is re-raising the exception.</code>

----

thanks you for your answer.
So, objects autoreleased will be released in the parent AutoReleasePool if there is an exception.

-- BVO.
----

