---
layout: page
title: InformationFromWeb
---

I want to be able to download stock quotes into my application. I don't know where to start. Is there any information anywhere on this topic.

Thanks!

----

http://finance.yahoo.com/d/quotes.csv?s=AAPL&f=sl1d1t1c1ohgv&e=.csv - replace AAPL with any other stock symbol.

----
DSG

*What's DSG stand for?*

Thanks! 
DSG is a signiture to look up posts.

Do I have to add a browser view to my application??? This http downloads the data into a file on the desktop or wherever. How do you send the request from you application and get the received information into your application???? Is there any documentation on this anywhere???

----
Look up NSURL. That should be able to do what you need, without adding a webview to your project (i.e. doing it in the source code)

