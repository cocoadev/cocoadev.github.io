---
layout: page
title: AppleScriptReturnValues
---

Hi,

I'm looking for a way to get variables from an applescript script into my app. I considered making my app applescriptable and then setting the variable directly in applescript. However I am writing a foundation tool (User Agent) so it isn't a .app and hence no Info-_____.plist file. So even if that would work I am not sure how to impliment it.

I'm really stuck on this one, I wish it was as simple as: int volume = [appleScript executeAndReturnError:nil];

----

It is that easy. NSAppleScript has -executeAndReturnError:, which returns the result of the script as an NSAppleEventDescriptor. Use -int32Value to get the number from the NSAppleEventDescriptor.

That answer was so RTFM, that I think you must have left something out.

----

Yeah that worked great, I really should've noticed that when reading the documentation. Thanks.

