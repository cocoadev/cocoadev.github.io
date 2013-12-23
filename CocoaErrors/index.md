---
layout: page
title: CocoaErrors
---

A page to post error messages, and the solutions that fixed them.

---- 

Cocoa itself do not have any errors. There are NSException and NSError classes that handles all such stuff. 
NSException is used when some of cocoa classes have reached error during runtime or when you have passed a wrong argument to a function.
It is vital for every developer to install custom breakpoint on each NSException raise in gdb, so you will have opportunity to catch a bug right inside the code which caused this exception. To add such breakpoint to gdb type in debugger window:

    break -[NSException raise]

For more information about handling exceptions in your code, check ExceptionHandling page.

----

NSError is the new cocoa class that helps some other classes to return more detailed error information indirectly, instead of just returning a nil value. For example, here is a NSError usage by NSXMLDocument class:

    - (id)initWithData:(NSData *)data options:(unsigned int)mask error:(NSError **)error.

You create NSError pointer and pass it to the initializer: 

    NSError *error = nil;
    [[NSXMLDocument alloc] initWithData:data options:0 error:&error]

-- DenisGryzlov

----

You also must not check the status of this function execution by examining *error != nil*. Some functions may set *error* to some undefined value during execution. Instead, check the return value. If it in NO or nil, *error* is a valid NSError object with more information. All instances of NSError are automatically autoreleased for you, so you don't need to release it after return, like you should do in NSPropertyListSerialization methods.

-- DustinVoss (well, DenisGryzlov said it, I just restated it more clearly)

----

What DustinVoss said.  No Cocoa (or third-party) methods should be modifying the value of a passed-in error argument unless an error actually occurs, but you should also not depend on that in your own code.

