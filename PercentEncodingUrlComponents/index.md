---
layout: page
title: PercentEncodingUrlComponents
---

Hi,
I have a string containing a URL address which itself has a URL component as shown:

http://www.google.com/search?q=COMPONENT

This component is simply an NSString which i need to percent escape before posting. An example of the components contents is as follows:

'cocoadev rocks!'

I have attempted to replace the space and exclamation mark with their respective percent-escaped values, %20 and %21 respectively.

Here is my code so far, which has not been working:

    
- (NSString *)urlEncodeValue:(NSString *)string {
	CFStringRef originalURLString = (CFStringRef)string;
	CFStringRef preprocessedString = CFURLCreateStringByReplacingPercentEscapesUsingEncoding(kCFAllocatorDefault, originalURLString, CFSTR(""), kCFStringEncodingUTF8);
	CFStringRef urlString = CFURLCreateStringByAddingPercentEscapes(kCFAllocatorDefault, preprocessedString, NULL, NULL, kCFStringEncodingUTF8);
	return (NSString *)urlString;
}


I have also tried the following

    
-�(NSString *)stringByAddingPercentEscapesUsingEncoding:(NSStringEncoding)encoding


but that method doesn't seem to encode all values, such as ! and + etc.

any ideas as to why this isn't working

----
I m not an expert, but I would guess as characters like "/", "+", "!" and so on are legal characters of an URI, they would not be escaped just so. This would make sense as you might (most will) pass CFURLCreateStringByAddingPercentEscapes a whole URL, including slashes etc. . Example:

ftp://super.ftp.server.com/folder/that one particular file.doc

will, in your given CF code snippet, be escaped to:

ftp://super.ftp.server.com/folder/that%20one%20particular%20file.doc

which to me looke perfectly fine!

If you want to escape such legal characters, according to the docs, you simply specify them in the fourth argument (legalURLCharactersToBeEscaped) of the function call and you should be fine!

--marcocoa

----

