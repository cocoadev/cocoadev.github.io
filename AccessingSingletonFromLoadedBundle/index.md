---
layout: page
title: AccessingSingletonFromLoadedBundle
---

I'm currently trying to build an application that uses loadable bundle plug-ins. 

My idea is to have a "Manager" singleton class that the plugin uses to register itself in the bundle class' +load method.

Something along these lines:

    
 //MyPlugin.m
 +load {
     ManagerClass* manager = [ManagerClass sharedInstance];
     [manager registerClass:[self class]];
 }


However, I have a little problem with this method.  Specifically, it seems that multiple manager instances are created, one for the bundle, and one for the host application.  A few quick debug statements show this:

    
 2004-04-04 14:44:10.817 Test[667] Created instance: <ManagerClass: 0x341810>
 2004-04-04 14:44:10.824 Test[667] Registering class PluginClass for manager <ManagerClass: 0x341810>
 2004-04-04 14:44:10.830 Test[667] Created instance: <ManagerClass: 0x33feb0>


Clearly to different instances of the singleton are being created.  The singleton is based on the most simple example over in SingletonDesignPattern.
:
I have found this site: http://freaky.staticusers.net/ugboard/viewtopic.php?p=27368#27368 which mentions a similar problem.  His solution was to not include the code (.m) in both the plugin and in the host application.  My plugin code does not include any code from the host app, and references the manager using a @class directive.  I have tried including just the "ManagerClass.h" file in the plugin target as well, with the same results.

I may break down and set the plugin class as the principleClass and have the host app call some known interface on each plugin, but I prefer having the plugins register with the host so that multiple plugin classes can be easily registered with the host.

Any help is appreciated.
Jake

----

I have no clue why it would be doing this, and I'm not sure how I'd be able to know with what you gave. Did you run your program in the debugger, setting a breakpoint on the method which creates ManagerClass? That seems like the drop-dead obvious way to find the problem to me. You're already logging it, so just set a breakpoint and see what's calling it and why.

----

Anytime you are NOT guaranteed that a method will be called only once (e.g. awakeFromNib, load, viewDidMoveToWindow...) you should test a condition before performing any setup:

    
 ManagerClass *ManagerClassSingletonInstance = nil;
 
 + load {
     if (!ManagerClassSingletonInstance) {
         ManagerClassSingletonInstance = [ManagerClass sharedInstance];
         [ManagerClassSingletonInstance registerClass:[self class]];
     }
 }


I'm assuming that     [ManagerClass sharedInstance] creates the shared instance when this method is called.

