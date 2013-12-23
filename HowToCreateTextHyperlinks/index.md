---
layout: page
title: HowToCreateTextHyperlinks
---

See especially NSTextViewAsWiki

I want to create hyperlinks in text documents so that clicking on a given link will bring the reader to another location in the same document, or in another document in the same Cocoa application.

----

If they do not have to be text documents, you could just use a WebView and HTML anchor tags.

Otherwise, you could set some kind of unique identifer on your target ranges, and set NSLinkAttributeName on the link ranges with the identifer as the attribute value, then use NSTextView's     textView:clickedOnLink:atIndex: delegate message and     attribute:atIndex:effectiveRange: to locate the link's target.

----

NSTextViewAsWiki shows how to make links in an NSTextView and how to respond to them... I would think you would change the code from automatically changing WordsJammedTogether into links that contain the words, but essentially what you want is textView textStorage] setAttributes:[[[NSDictionary dictionaryWithObject:linkValue forKey:NSLinkAttributeName] range:someRange]; and implement textView:clickedOnLink:atIndex: in the text view's delegate to handle clicking the link (note the link argument to the delegate is the linkValue you set in the dictionary when you created the link...

