---
layout: page
title: DOScriptingArchitecture
---

AppleScript is really nice... really, I like it and have used it to create small launcher-y scripts to full applications... But it has some serious problems too... The dictionary that you get from applications is supposed to tell you what commands an application supports and make it clear how to use it to script with... For this task it utterly FAILS... The syntax is often guess-work for the user, and for the provider, well... lets just say I haven't found a very clear tutorial on making AppleScriptSupport a reality in my application... it's just too much work!  Here's my proposed solution... Have applications vend objects using DistributedObjects, register them with the bundle identifier, publish the api with a header in the bundle that declares a supported protocol, and then the external client uses DO to connect and use the interface.  This sounds like it limits things only to ObjC... but it doesn't.  FScript would be great at this; perl, ruby, and python all have access to Cocoa interfaces as well, and an AppleScript wrapper isn't out of the question either.  For the scripting provider, you do almost nothing to support scripting... the following suffices:

    

[[NSConnection defaultConnection]  setRootObject:scriptableObject];
[[NSConnection defaultConnection] registerName:@"com.me.bundleId"];



then you publish the protocol in a header file:

    

@protocol ComMeBundleIDScripting
-(void)someMethod; // does something
@end



and here's an example of FScript accessing and using this object...

    

bundleId := 
    NSConnection rootObjectForConnectionWithRegisteredName:
        @"com.me.bundleId" 
        host:nil.
bundleId someMethod. "performs remote method call..."



For AppleScript-ers there could be tools to take that protocol and parse it into a nice dictionary for them, and allow them to use it kinda like:

    

tell distributed object "com.me.bundleId"
    perform action "someMethod"
end tell



Application coders benefit from easily adding scriptability, AppleScript-ers benefit from more apps having scripting interfaces, users of other scripting languages benefit from having scripting interfaces they can use without having to hack together a way to generate raw AppleEvents.  I use this for my own projects, and it would be great if other people did too as a community building this kind of scripting infrastructure, but the real hope is that someone at Apple reads this and gets intrigued.... (like vending objects for "com.apple.iTunes", "com.apple.Safari" etc...)

If you are interested in helping build this infrastructure more widely visit DOSAStrategy

----

PostScript: Some of you might be wondering about security issues... DO allows remote objects to message across the network right... well the defaultConnection is set up to use MachPort-s which are local machine only... (unless you want to vend the object over the network... which is only a line or two more code).

PostPostScript: The protocol declaration is actually unnecessary too, as long as the client knows what methods the vender vends...

