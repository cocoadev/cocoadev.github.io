---
layout: page
title: StrangeBehaviorinNSURL
---

Hello i was trying to use a NSURL like this :
     
NSURL *dicturl;
General/NSDictionary *productVersionDict;

url = @"http://somehost.dyndns.org/stuff.xml";
dicturl = [NSURL General/URLWithString:url];
productVersionDict = General/[NSDictionary dictionaryWithContentsOfURL:dicturl];

if (productVersionDict != nil) {
// do stuff here
}
else {
	General/NSLog (@"error can not load url : %@\n",url);
}

It seems that it is impossible to load this url. But any other url is working. But in my browser i have no problem to access this url either with/without proxy.

So i guess that NSURL is not working when the specified URL has a wrong reverse name lookup !

Can anybody verify my presumption?

-- General/KarstenFuhrmann

----

From my understanding, the XML file must be an Apple Property List-type file in order for General/NSDictionary to understand it.

-- Yuhui

----

Sorry, I think I posted in the wrong place.
