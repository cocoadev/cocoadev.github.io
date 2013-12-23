---
layout: page
title: HowToCreateWidgetForSafariPlugIn
---

Hi,
   I need to create a widget which acts as plug-in for Safari. I had gone through documentation and found a webkit framework can be used as plug-in to safari. But I need to create a widget and use this widget as a plug-in to Safari. So please, could you tell me is it possible to have a widget which acts as plug-in for Safari? If yes, please give me the details.


----

Can you explain this some more? I don't really understand what "a widget which acts as plug-in for Safari" means. The word "widget" in Mac development generally refers to Dashboard widgets, and Dashboard has nothing to do with Safari other than also being based on WebKit.

----

"Widget" is a term that's nearly meaningless absent some sort of context. If you're going to use that word, you'd better let us know what you think it means. As far as creating plugins for Safari goes, you have several options. The first is to write a web browser plugin using the traditional Netscape plugin API. If you do this, your plugin will work with most browsers, not just Safari, and you'll be in a better position to port your plugin to other platforms if you ever decide to do that. The second option is to use the WebKit API, which is probably easier and better if you don't mind targetting only WebKit-based browsers (including Safari). The third option is to write a SIMBL plugin that targets Safari. This is a very powerful technique, but for the most part you're on your own as far as figuring out what you need to do and how to do it. It's also the least compatible technique in that your plugin will be bound to break as Apple makes changes to Safari.

If you really are intending to create a Dashboard widget that integrates with Safari, I'd guess that you could combine a plugin created using one of the techniques above with a typical Dashboard widget. It's hard to understand why you'd need to do that, however, since Dashboard widgets can access the net on their own, do their own display, etc. -CS


----

Thank you. I will try with the above techniques with a Dashboard widget.

