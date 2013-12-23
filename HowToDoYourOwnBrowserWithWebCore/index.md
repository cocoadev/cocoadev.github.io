---
layout: page
title: HowToDoYourOwnBrowserWithWebCore
---

This tutorial is obsolete as of Safari 1.0.

A new, easier tutorial is to find at HowToDoYourOwnBrowserWithWebKit

*Whaa? Nothing there. We have SomeWebKitQuestions though...*

----

If you dig through Safari and get browser.nib, you can use the Safari's brower window with some work.

And also has anyone else found that you MUST put http:// before the url for it to load?

-- JacobHazelgrove

Well, it's no suprise that you have to put http:// before the url; it's a Framework for url-loading, not a fool-safe browser.. EnglaBenny

----

You can do something like:
if (![urlField hasPrefix:@"http://"]) [urlField setStringValue:[NSString stringWithFormat:@"http://%@", [urlField stringValue]]];
This is how I'm adding shortcut searches to my web browser. 
-- San

----

To take San's code a little further, here's how to autocomplete URLs more or less how Safari does it.

 changes "www.apple.com" into "http://www.apple.com"
 changes "apple" into "http://www.apple.com"
 changes "apple/support" into "http://www.apple.com/support"
 changes "oldjewelsoftware/ppicker" into "http://www.oldjewelsoftware.com/ppicker"
 changes "oldjewelsoftware/ppicker/releases.html" into "http://www.oldjewelsoftware.com/ppicker/releases.html"
 leaves urls with other protocols (mailto, ftp, etc.) alone

    
	NSString* urlString = stringToUse;

	NSURL* theURL = [NSURL URLWithString:urlString];
	
	if (!theURL scheme] length]) 
	{
		[[NSArray* stringParts = [urlString componentsSeparatedByString:@"/"];
		NSString* host = [stringParts objectAtIndex:0];
	
		if ([host rangeOfString:@"."].location == NSNotFound)
		{
			host = [NSString stringWithFormat:@"www.%@.com", host];
			urlString = [host stringByAppendingString:@"/"];
			urlString = [urlString stringByAppendingString:
			stringParts subarrayWithRange:[[NSMakeRange(1, [stringParts count] - 1)] 
                                  componentsJoinedByString:@"/"]];
		}
			
		urlString = [@"http://" stringByAppendingString:urlString];
		theURL = [NSURL URLWithString:urlString];
	}
		
	myWebView mainFrame] loadRequest:[[[NSURLRequest requestWithURL:theURL]];

-- a sharp old jewel

----
How to use WebKit to display a web page without network nor disk access (ie. no http: or file: URL protocols)?

-- Stephane Boisson
----
- (void)loadHTMLString:(NSString *)string baseURL:(NSURL *)URL 
String is the HTML source of the page and baseURL is the basis for relative links.

-- Hasan Diwan
----

