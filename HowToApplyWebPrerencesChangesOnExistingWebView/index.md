---
layout: page
title: HowToApplyWebPrerencesChangesOnExistingWebView
---

According to Apple, updating an existing WebView's preferences doesn't take effect immediately (at least not until a reload), but I've seen some apps be able to change the webview's preferences and make them take effect before a load into its frame occurs. I've not been able to accomplish this. Is there a special trick to it?

----

Yes, unfortunately there is a special trick.

    
WebPreferences* customWebPrefs;
// assume you get this init'd etc somewhere 

[_webView setPreferencesIdentifier:[customWebPrefs identifier]];
[_webView setPreferences:customWebPrefs];
// note: for some reason the doesn't do the appropriate notification, so I have to force it here
// this is the 'trick'
[customWebPrefs _postPreferencesChangesNotification];


Hope that gets you on your way.  --MikeSolomon

