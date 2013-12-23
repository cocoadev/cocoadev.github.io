---
layout: page
title: PreventingNSColorPanelFromShowing
---



I have a slightly unusual situation which I'd like to receive suggestions about.

I have an instance of NSColorWell. When its     -activate: method is called, the normal behaviour is to order the NSColorPanel to the front, as well as (optionally) deactivate any other NSColorWells. I want to keep the deactivation of other wells intact, but get rid of the opening of the panel. The reason is that all I really want from my code is to deactivate any and all NSColorWells that might exist in my app at a certain time, but there is no method available to do this, so I'm using a hidden NSColorWell to make this happen for me by activating it.

Seems the only way is to resort to some sort of hack, but which? I can listen for window notifications from the color panel for example, and knowing I'm calling the well's activate method I could do something like move it offscreen. I have tried this and it works sort of ok, but sometimes the window is visible for a very brief moment, which is not acceptable. That's also using an undocumented notification:     _NSWindowDidBecomeVisible. There has to be a better/cleaner way.

Any ideas? --GrahamCox

----
I hesitate to suggest this in case there's a real answer out there, but...get the shared NSColorPanel and set its alpha to 0.0. You may also have to     setOpaque: to     YES. Then     activate:,     orderOut:, and reset the alpha (and opacity if necessary). It's a real kludge but the user probably won't notice anything unless they somehow manage to click on the invisible panel before it leaves again. --JediKnil

----

I have to admit, it works really well (another undocumented notification,     _NSWindowWillBecomeVisible allows me to save off the existing alpha and set it to 0). But yes, there's no doubt it's a kludge ;-) I may go with it if there are no better ideas. --GC.

----

Following up on your comment that what you *really* want to do is disable all extant NSColorWell instances at any time: a long-handed but less kludgish way of doing this might be to hook into NSColorWell's     alloc and     free methods using ClassPosing or MethodSwizzling, and maintain an NSMapTable of all extant instances... you could then do whatever you wanted to every/any instance, whenever you wanted. I suggest NSMapTable because it allows you to store pointers without retaining or releasing them (a sort of weak reference, I suppose). -- ToM

----

OK, I was going to try this exactly as suggested but then I realised that ClassPosing allows me to solve it even more cleanly. All I needed to do was override the     - activate: and     - deactivate methods and add some notification postings there. I can also track the current selected well using a static var and turn it off when necessary without having to activate another (hidden) well. Seems like a very clean solution, and works like a charm. One question - since this code is buried in a framework and I'd prefer not to insist that everyone modifies their main() function, I set up the class posing in my poser class's     + load method. Seems to work - but is there likely to be a problem with it? --GC

----

I've done something similar in one or two of my projects and had no problems. 

The only gotcha you're likely to have is that, if you want to use the same technique several times for the one class in any one application, you can't depend on your multiple     +load methods being called in any given order. This problem arose for me because I had a whole bunch of plugins that used class posing to provide optional functionality, and it meant that plugin Y couldn't rely on plugin X's customised methods being available at load time even if they'd both be loaded eventually. The workaround is to make sure that there's nothing in your     +load method that depends on anything outside the class it's loading. --ToM

