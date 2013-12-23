---
layout: page
title: SettingSizeOfWebviewProgrammatically
---



How do I change the size of a control (in this case a WebView) programmatically? It's probably something stupidly simple, but googling, searching this site, and searching the documentation yielded nothing.





Thanks!

Use NSView's     -setFrame: method.

----

Ok, I tried programming this, but I'm a little lost here. I can't get this code to compile:

    
NSRect aFrame;
  
    aFrame = [WebView frame];
    
    aFrame.size.height = aFrame.size.height + 10;
     
    [WebView setFrame:aFrame display:YES animate:YES];



Please note this code is based on my window resizing code.

EDIT: GAH, this was my own stupidity. It was referring to the WebView class rather than my webView control... anyway, it compiles now, but when I try to fire the action is simply reports in the run log "+[WebView setFrame:]: selector not recognized". Eh?

EDIT: Nevermind again, once again my stupidity. Here's the working code if someone might want to use it:

    
    NSRect aFrame;
    aFrame = [webView frame];
    
    aFrame.origin.y -= aFrame.origin.y;
    aFrame.size.height = aFrame.size.height + 20;
    
    [webView setFrame:aFrame];

