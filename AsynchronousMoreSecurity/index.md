---
layout: page
title: AsynchronousMoreSecurity
---



I have been using Apple's M<nowiki/>oreSecurity library to run a privileged tool from a Cocoa application. The only problem I've run into is that     M<nowiki/>oreSecExecuteRequestInHelperTool does not return until the tool completes the requested operation. For lengthy operations, this means that the application will become unresponsive and the spinning wait cursor appears.

In an effort to correct this problem, I created a few new functions that allow M<nowiki/>oreSecurity to be used in an asynchronous fashion. The source code has been uploaded to my home page along with a sample test similar to Apple's MoreAuthSample.

http://homepage.mac.com/brianwells/.Public/TestAuthTool.zip

Please let me know if there are any serious flaws to this approach or if any of you have a better technique to using M<nowiki/>oreSecurity asynchronously.

Thanks,

� Brian Wells

