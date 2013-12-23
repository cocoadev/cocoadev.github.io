---
layout: page
title: NSTableViewReloadDataConcurrencyIssues
---

I am using Cocoa as implemented in java, however I am running into some issues.

I have a class that implements both NSTableView.DataSource and BrowseListener from the com.apple.dnssd library.  I start a browse listener and when a service is discovered it's added to the data source, and the table is reloaded using reloadData().

However the table does not actually reload every time data is added to the data structure.  I am thinking it is because the member function for the BrowseListener implementation is calling reloadData() on the table is being called from multiple threads (due to multiple MDNS responses) and too frequent of calls are just ignore.  I know the data structure is being updated because the next call to reloadData() fills in the rest of the table.  I created a workaround function that only allows the table to be updated at most once every 50 milliseconds, but I am wondering if this is a known issue with the java cocoa implementation or if there is a more conventional way of implementing what I am trying to do.

----
AppKit *is not thread safe*. Calling reloadData from any thread other than the main thread is wrong and will lead to odd behavior and crashes. You must do all GUI work on the main thread.

----
Also, you are *not* "using Cocoa as implemented in Java" -- you are using Cocoa *from* Java.  There's a huge difference; there are not two implementations of Cocoa on your system.

