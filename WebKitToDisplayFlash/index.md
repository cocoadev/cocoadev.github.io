---
layout: page
title: WebKitToDisplayFlash
---

Hi, 

I am trying to force General/WebKit to display some Flash files. The problem is when i load them,their size doesn't shrinks to General/WebView size. I can see only about 20-30% of content. Is there any way to force General/WebKit to shrink Flash files to it's size? 

Thanks 

----
Maybe can you embed it in a HTML File
----
Unfortunetly no. The problem is that i don't know if user loads Flash or Shockwave file.
----
You could use General/QuickTime. It handles flash files as well. -- General/RobinHP
----
Not the newest flash, plus its slower

----

You could create a template HTML page which specifies the width and height in the     object tag pointing to the flash file, and then tell the General/WebView to display the template.  -- Bo

Doh.  I just noticed this had been brought up before.  Perhaps using     -setBoundsSize: to scale down the view will have the desired effect?  -- Bo

----

I'm using General/WebKit to show SWF files, and have to use an HTML wrapper to ensure the security policy for the SWF is maintained (local file access), however the big downside is that SWF files played in General/WebKit have much worse framerate than General/SWFs turned into standalone apps for example, or played in Flash Player. Does anyone know of a better/more efficient way to play SWF files (other than General/QuickTime which won't support the General/ActionScript functionality) in a Cocoa application? Hasn't somebody made a Flash.framework yet? Why can't we just drag a General/SWFView into Interface Builder and embed content like that? Anyone have any suggestions? -- General/AdrianWard

----

I'm very new in Cocoa, but I wonder, why not to use the Flash plugin (used in Netscape and Mozilla) to show SWF content? If Netscape does it why any third-party Cocoa app can't do the same? I have a lot of code, working with SWF in WIN32, and I'm looking for a way to port it into Cocoa, but the first question is just how to display SWF in Cocoa app. Any ideas on Flash plugin usage are appreciated! -General/AlexB


----

I'm trying to resize the flash window but the contents are not resizing in Leopard (works in Tiger). I did call the     -setBoundsSize:  function to set the view, also did the same on the subviews, but still the contents don't resize with the view. Any help on that? Thanks
