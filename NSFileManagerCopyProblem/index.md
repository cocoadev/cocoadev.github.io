---
layout: page
title: NSFileManagerCopyProblem
---



I am having a strange problem with NSFileManager.

I attempt to do a:

- (BOOL)copyPath:(NSString *)source toPath:(NSString *)destination handler:handler

Now, I can get it to copy from my main HD to my main HD. I can also get it to copy from my other HD's to my main HD. I cannot though get it to copy from any old HD to another HD. Is this a permissions error or some bug that I do not know about?

-- Mat

----

Have you installed a handler object and asked it for the error that occurs? If you install a handler object you'll get a fileManager:shouldProceedAfterError: message if something bad happens. Printing out the error dictionary you get from this may be instructive.

-- ChrisParker

