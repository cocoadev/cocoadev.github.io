---
layout: page
title: DocumentBasedCoreDataAcrossMultipleNIBs
---

I'm new to making cocoa development and am not too familiar with this, so I would really appreciate the help.

The problem:
In my application, there are two windows in two different NIBs. The main window has the basic the document meat, but the other window *should* be displaying attributes of the items selected in window one. So my question is, how do I get selections in different NIBs to respect each other?

I did a search and found a really relevant topic, but I wasn't quite sure if it applied to document based applications as well.

Here's how the app is basically used:
User selects item in window one --> window two displays attributes of item from window one
The windows are in separate NIBs because both windows switch content.

