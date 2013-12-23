---
layout: page
title: FlowChartView
---



I was recently playing around with the Xcode data/class designer and was wondering how those neat flow chart views were made. I then found an even cooler implementation of a flow chart in the Quartz Composer app that comes with recent versions of the developer tools. Does anyone have any hints on how to create something similar (but simpler :P)


-DanielPeebles

----

I tried a NSView subclass of my own : EFStringView. Still has some work to be done, but already pretty cool.

You will find a demo file here : http://homepage.mac.com/edouard.fischer/ (deprecated, see below).

-EdouardFischer

*(for readability and load speed of this page I removed the old image) --LoganCollins*

----

Oohhh... nice! By what license can we use your code? - RbrtPntn

----

new BSD license http://www.opensource.org/licenses/bsd-license.php , and send me a poscard on email at edouard.fischer@mac.com if you like it or if you made improvements.

-EdouardFischer

----

I posted a new release here : http://homepage.mac.com/edouard.fischer/ (deprecated, see below)

It is called EFLaceView. I changed a lot of names, because in the previous release, everything was named "string", and that was confusing. Now we have laces and holes, like on your shoes.

Thanks to RbrtPntn, the look and feel is now very good.

-EdouardFischer

----

New release here : http://edouard.fischer.free.fr/EFLaceView.dmg.zip

----
The link above is broken. Any pointers?

-HishamKhalifa

----
See http://stackoverflow.com/questions/3071832/any-cocoa-control-code-that-i-can-use-that-acts-as-a-patch-bay

