---
layout: page
title: ClickableUrlInTextView
---



This is derived from a mail by Peter Ammon on the MacOsxDev list:

Since ParsingHtmlInTextView is possible, wouldn't it be nice to be able to display clickable links, as well?

As it turns out, it's fairly easy.  No subclassing necessary.  Get the NSTextStorage of your NSTextView and set the NSLinkAttributeName attribute of the URL in the string to the address of the web page.  Then implement the 
textView: clickedOnLink: atIndex: method in the NSTextView's delegate.

Here's some basic untested sample code. It should work alright, but please correct any typos or other errors you encounter. (To make it truly usable with HTML, we should change the Url-Recognition from so that it looks for href-Tags rather than http.)


    
- (void)hiliteAndActivateURLs:(NSTextView*)textView {

	NSTextStorage* textStorage=[textView textStorage];
	NSString* string=[textStorage string];
	NSRange searchRange=NSMakeRange(0, [string length]);
	NSRange foundRange;

	[textStorage beginEditing];
	do {
		//We assume that all URLs start with http://
		foundRange=[string rangeOfString:@"http://" options:0 range:searchRange];

		if (foundRange.length > 0) { //Did we find a URL?
		  NSURL* theURL;
		  NSDictionary* linkAttributes;
		  NSRange endOfURLRange;

		  //Restrict the searchRange so that it won't find the same string again
		  searchRange.location=foundRange.location+foundRange.length;
		  searchRange.length = [string length]-searchRange.location;

		  //We assume the URL ends with whitespace
                  endOfURLRange=[string rangeOfCharacterFromSet:
                     [NSCharacterSet whitespaceAndNewlineCharacterSet]
		     options:0 range:searchRange];

	         //The URL could also end at the end of the text.  The next line fixes it in case it does
	         if (endOfURLRange.length==0)  // BUGFIX - was location == 0
                   endOfURLRange.location=[string length]-1;

	         //Set foundRange's length to the length of the URL
	         foundRange.length = endOfURLRange.location-foundRange.location+1;

	         //grab the URL from the text
                  theURL=[NSURL URLWithString:[string substringWithRange:foundRange]];

	         //Make the link attributes
	         linkAttributes= [NSDictionary dictionaryWithObjectsAndKeys: theURL, NSLinkAttributeName,
                      [NSNumber numberWithInt:NSSingleUnderlineStyle], NSUnderlineStyleAttributeName,
                      [NSColor blueColor], NSForegroundColorAttributeName,
                      NULL];

	         //Finally, apply those attributes to the URL in the text
	         [textStorage addAttributes:linkAttributes range:foundRange];
		}

	 } while (foundRange.length!=0); //repeat the do block until it no longer finds anything

	[textStorage endEditing];
}



Now, in the delegate for your NSTextView, simply add this:

    
- (BOOL)textView:(NSTextView*)textView clickedOnLink:(id)link 
atIndex:(unsigned)charIndex {
     BOOL success;
     success=[[NSWorkspace sharedWorkspace] openURL: link];
     return success;
}



Note that this works for single clicks, not double clicks.  But users 
are used to single clicking on links, so maybe that's better.

----

I just tried this, and it seems to work well.  Does anybody know how to make the cursor turn into the pointing finger when it goes over a URL, using this method?

-- StevenFrank

----

I've posted some code to do cursor and click tracking in URLs at http://www.blackholemedia.com/code/

-- AaronSittig

----

One bug fix - if the URL's at the end of the string, then rangeOfCharacterFromSet:options:range: returns {NSNotFound,0}. NSNotFound is not 0, so the little test afterwards fails and an exception gets raised by the substringWithRange later on.

-- ChrisRidd

----

There also is "official" sample code at http://developer.apple.com/samplecode/Sample_Code/Cocoa/TextLinks.htm

But it doesn't live update cursor rectangles either. I need that. Is it OK to call resetCursorRects from textDidChange? Or could I avoid addCursorRect altogether by using a timer as Apple's example does for showing attributes? Can I use the area from ImplementSyntaxHighlighting?

-- TobiasWeber

----

The link provided by TobiasWeber above has been updated on 2003-VII-28.  It now has all sorts of implementations, cursor changes, etc.  People should date their posts.

-- DanielToddCurrie 2003-X-20

----

If you want the background to be transparent, instead of white... This should give the answer: TransparentNSTextViewBackground

-- TheodoreHSmith 2005-7-18

----

Note that the text view must be selectable, otherwise clicking on links won't do anything.

-- DavidCatmull 2005-10-11

----

For the cursor, why not simply set the NSCursorAttributeName attribute to [NSCursor pointingHandCursor]?

----

You can create static URLs in NSTextViews without any coding by copy/pasting text with URLs from Microsoft Word (or other apps that put RTF on the clipboard) into Interface Builder.

-- MattBendiksen 2006-04-09

*TextEdit also has the ability to make links in rich text, at least on 10.4 (Format->Text->Link...).*

----

When a clickable URL is placed at the very end of the document, any and all text typed at the end will also be covered by that link attribute.  This may be desirable behavior for other attributes, but it's rather a hassle for links.  I'm thinking of handling it by having my implementation of -textDidChange: keep track of whether the last character was covered by a link attribute the previous time it was called and trim the range of the link attribute accordingly.  Is there a better way I'm missing?

-- SeanUnderwood 2006-06-15

----
Watch for selection changes and use setTypingAttributes:, maybe?

----
Removing any NSLinkAttribute from the typing attributes whenever the text changes did the trick.  Thanks.

----
The most intuitive behavior for the user will probably be to remove that attribute when at the end of a link (and the beginning, if you even get the attribute), but to preserve it when in the middle.

