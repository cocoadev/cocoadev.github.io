---
layout: page
title: TemplateTab
---


I've created a tabView and am trying to put a custom view inside it. This tab is to serve as the model for tabs created afterwards. So, I tried this:
    
General/NSTabViewItem *item = General/[[NSTabView selectedTabViewItem] copy];

It seems that General/NSTabView does not conform to General/NSCopying. So I then tried.
    
General/NSTabViewItem *item = General/[[NSTabViewItem alloc] init];
[tabView addTabViewItem:item];

This didn't copy the view inside the original General/NSTabViewItem to the new one. Finally, I tried:
    
[tabView addSubview:myView];
General/NSTabViewItem *item = General/[[NSTabViewItem alloc] init];
[tabView addTabViewItem:item];

This crashed the application. So I'm at a loss and hoping someone can enlighten me as to what's wrong. If you do post an answer, also forward it to me by email at hdiwan-at-mac.com as I don't usually check this site. Cheers!
----
The step I was missing was that I need to call [item setView:myView] after creating the tab. All's fine now! -- Hasan
