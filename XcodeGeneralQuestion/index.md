---
layout: page
title: XcodeGeneralQuestion
---

I am new to cocoa. The only coding I have done is with Actionscript, CSS, HTML, some General/JavaScript, some XML and PHP. I am trying out the core data feature in xcode and have made a few things. Sometimes my apps wont work on any computer except the one it was created on. What exactly would contribute to that in the build settings?

email me at info@shadelandstudios.com if you have any helpful information.

Thanks, 
Robert

----

See General/HowToAskQuestions and General/HowToUseThisSite. In particular, give more details about what's happening, including console output from the computers that aren't working. Also, asking for replies by e-mail is considered impolite, since it implies that you can't make the time to come back and check the site, but you are willing to use the site's resources to get what you need.

----

In addition, make sure you set your build configuration to "Release", rather than "Development" (or is it called "Debug" now?) - the problem you describe is a common one. General/ZeroLink is enabled and that executable will only work on your computer. Please RTFM; this is all in the General/XCode documentation!

----

One more thing: if you're developing on an Intel-based Mac, but testing on a General/PowerPC-based Mac, your binaries won't run unless they're built universal. See Apple's General/UniversalBinary documentation for how to do that.

----

I understand what you are saying about not coming back and asking for people to email me. I just wasnt sure if I'd be able to find this topic. This is a very neat/strange site that I could see me being a part of. I think the build configuration is the problem. Thanks. I will post my finds on this topic.

----

Thanks everyone. The zero link parameter was the problem. I did look at the Xcode documentation, but couldn't think of the correct search terms to find the answer I was looking for. Thanks again and I look forward to contributing to the site.
