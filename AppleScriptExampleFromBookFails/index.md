---
layout: page
title: AppleScriptExampleFromBookFails
---



I hope it's okay to ask questions here...  If not, sorry, and where should I go for that?

Here's the deal:  I purchased "Cocoa Programming for Mac OS X" recently, and flipped to chapter 28 on AppleScript implementation in your Cocoa apps.  I followed Mr. Hillegass's instructions as closely as possible and still pertaining to my own app, which is still very simple (change of a property name or two was all that was needed).

Roughly, I have this setup
    
 <header crap>
 <classes>
    <Application>
       <Attributes>
          bnumber
 </close everything appropriately from here...>

in my scriptSuite file, and similar information in my scriptTerminology file.

In my app's delegate class code, I have these methods:
    
 - (BOOL)application:(NSApplication *)a delegateHandlesKey:(NSString *)key {
   if([key isEqual:@"bnumber"])
     return YES;
   else
     return NO;
 }
 
 - (NSString *)bnumber {
   return [numberField stringValue];
 }
 - (void)setBnumber:(NSString *)s {
   [numberField setString:s];
 }

Using the AppleScript
    
 tell application "MyApp"
   set var to bnumber
   set bnumber to "text"
 end tell

I can get the information JUST fine from the text field.  Nothing, however, shows up/changes when I set the text (see line 3 of the script segment)...

I'm sure it's a simple problem, but if anyone could help out, that'd be great!

Or, let me know what else you need to see code-wise to help me.

--AberrantWolf

----

You might want to check the publisher's web site for errata for that page of that specific book. You might just find a mistake. I don't know AppleScript well enough to determine that either way, but it's always worth a try and might save you the wait for a response.

BTW, for consistency, please follow the rest of the community's example and put the begin/end entry tags at the top of the page. Nobody looks for the section headers at the bottom of a page anywhere. ;-)

*I checked the publisher (Addison Wesley) and there's no errata or anything on the book in question, but thanks for the advice, and, um... *bump*...?*

Actually, I have the book, and I can tell you that you should look at the author's site - bignerdranch.com - for the errata for both versions of that particular book. He also has a discussion board about the end-of-chapter challenges and particular pages. Quite a few people have posted there, so it's fairly useful if you're stuck on that book, not just for your particular issue, but for the whole book. -- JasonTerhorst

