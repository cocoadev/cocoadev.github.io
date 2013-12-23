---
layout: page
title: DisplayACertainPartOfAWebPage
---



I want my user to be able to press a button (in other words I want to use an IBAction) and for a certain area of a webpage to display.   Also I want the links inside the page to open up with Safari. I want to be able to scroll as wel.  Is there anyway to do this with Webkit?

----

WebKit will display whatever HTML you give it. You haven't defined where you're getting the source web page, but you can always scan through it using DOM (included in WebKit) for the part of the web page you want and hand just that segment (or a new HTML document with that segment inserted into the body) to your web view for display.

For future reference, please read and follow HowToAskQuestions and avoid creating MailingListMode pages. Good behavior is rewarded with good help.

