---
layout: page
title: CreateDeploymentApp
---



How do you create a simple distributable Application with Xcode? I have tried to do this but when I move the application to another computer, it starts up and bounces and then goes away with a err=22 message in the console log. When I build the application in Xcode, I make sure its on deployment mode and ZeroLink is off just like I've found in other instructions. I'm running Xcode 1.5 on a G4. 

----

1. Change build style to Deployment in **target settings**

2. Clean project

3. Build.

If that's not working, then you have a bug in your application, or your project file references are messed up.

----

For instance. If I do a simple application with the name 'simple'. I get an application in the build folder with generic icon and the name 'simple'. I also get folder with name 'simple.build'. Is that used for anything? Should be able to copy the application from there and put it on mac that doesn't have developer tools installed and run it? 

I've done the steps above already and it still doesn't work. Are there some other settings or file references that need to be tweaked?

*Yes you should and no there aren't. Please give more details, such as whether anything shows up in Console, and exactly what it says.*

----

Thanks for your help. I think I know why it wasn't working. I was building my apps on a G4 with 10.3 and was trying them out on a G3 running 10.2.8.  I went back and tried thek on my Cube and they work. I guess I have to figure out how to create a deployment setting for G3's.

*it has nothing to do with G3 vs. G4, it's the 10.2 vs. 10.3 thing. build for 10.2 using the 10.2 SDK (again, in the target settings)*

----

Did you read the Console when your app didn't work? It most likely logged the reason that it couldn't start.

