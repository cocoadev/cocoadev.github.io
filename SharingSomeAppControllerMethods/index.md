---
layout: page
title: SharingSomeAppControllerMethods
---

Hey guys, very new to Obj-C >.<

I've written a Chess game in Obj-C. Now I'm writing the UI to it. I've been following the Hillegas book to learn Cocoa. 

To cut to the chase:

I want to send some text, @"Hello", to an NSTextView, which is declared in my AppController class to handle UI stuff, *devTextView, from my other source files. 

I will be calling this method from the AppController:

- (void)appendToDevLog:(NSString *)someString;

This handles appending text to the NSTextView. 

How do I call this method, without making another instance of my AppController class, in my other source files in the project?

----
See MailingListMode, HowToUseThisSite, TextFormattingRules, and *don't post the same question on two different pages*.

