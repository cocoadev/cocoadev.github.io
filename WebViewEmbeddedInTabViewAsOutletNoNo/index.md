---
layout: page
title: WebViewEmbeddedInTabViewAsOutletNoNo
---

Is it just me or it is just impossible to use Interface Builder to connect a controller's outlet to a General/WebView that is embedded in a <code>General/NSTabView</code> ? The General/TabView is always on my way, and it doesn't matter how I try to position the General/WebView... bringing it to front won't help either... the only way to connect it is keeping it out of the tab, but if I cut  it and paste it back, the General/WebView will lose the connection...

Any idea?

----
Set the nib window to outline mode and drag your connections there. You can switch between outline mode and the normal, icon mode using the little buttons just above the scroll bar. -CS
----
Or you can take out yout webview by clicking down and holding until pops out with a darker than usual shadow. Move it to the main window or an area which is unobstructed. Then connect the outlet to the webview. After that, click down and hold the webview until it pops out again. Then move it back into the tab view. Moving objects in this way keeps connections and attributes set during moving.

----
Thanks for the suggestions! I chose working in outline mode.
