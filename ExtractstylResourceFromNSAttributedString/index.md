---
layout: page
title: ExtractstylResourceFromNSAttributedString
---

I'm working on an application that will handle the creation and attaching of rich-text SLAs (Software License Agreements) to UDIF Disk Images.

I used the example found here: http://developer.apple.com/sdk/index.html to get started.  Instead of using DeRez to decompile the resource into a string, I created a .r file from scratch and entered in the text I needed like so (just save the entire code as plain text with Western (Mac OS Roman) encoding):

    

/*Include all of the resources we'll ever need*/
#include <CoreServices/CoreServices.r>
#include <ApplicationServices/ApplicationServices.r>
#include <Carbon/Carbon.r>
#include <QuickTime/QuickTime.r>

data 'LPic' (5000) {
	$"0000 0002 0000 0000 0000 0000 0004 0000"
};


data 'TEXT' (5000, "English") {
	"This is the meat of the SLA."
	"\n\n"
	"This is where I need to incorporate rich-text"
};

resource 'STR#' (5000, "English Buttons") {
    {   "English",
        "Agree",
        "Disagree",
        "Print",
        "Save...",
        "If you agree with the terms of this license, click \"Agree\" to access the software.  If you do not agree, press \"Disagree.\""
    }
};


/*code for incorporating other languages.*/



What I seem to be missing, though, is a way to incorporate a 'styl' resource into my resource file.  If anyone knows how I could extract that data from an NSAttributedString or something similar, I would be eager to learn.  I've tried looking through all the old documentation on the subject, but all I can find are resources for the depreciated TextEdit text handling system.

So far, I'm using an NSTextView for my input, but I can change that if need be.  Any help would be appreciated, thanks.

- Corporate Newt

----

There is no built-in way to do it. The attributes of NSAttributedString are stored in terms of dictionaries of properties and ranges, whereas the old 'styl' data is a far simpler pascal-based data structure with nowhere near the flexibility and richness of NSAttributedStrings. About your only hope is to manually parse the attrbuted string data and build a style record yourself with those bits of information than can be used, and discard the rest. (Also, having created style records programatically myself in the very distant past, they can be tricky to get right). But maybe there's just a better solution to the main problem - why not just use a rtf file which you can write out from an NSAttributedString in one line? --GrahamCox

----

I was hoping that I could accomplish something similar to DropDMG's Software License Agreement creation.  I'm currently writing a program that will create disk images via drag and drop.  My goal is to create a piece of free software that compares to some of the better shareware programs out there.  I was just hoping to be able to add support for rich-text SLAs in my app.  Thanks for the help though.  If there's any documentation that can point in me the right direction of how to manually parse the attributed string data into a style record, or what the make-up of a style record looks like, I'd appreciate it.

- Corporate Newt

----

The attributes of an NSAttributedString are straightforward - for example you can use     - (NSDictionary *)attributesAtIndex:(unsigned)index effectiveRange:(NSRangePointer)aRange which as you can see returns a dictionary and a range. The keys and values in that dictionary are well-documented and consist of things such as     NSFontAttributeName which is the key for an NSFont object, etc. etc. Thus you can retrieve all attributes for a block of text by repeatedly calling this method using the returned range to know how far to skip ahead to the next attribute change. The style record is an altogether more tricky proposition. It was documented in the OLD "Inside Macintosh" series as part of the TextEdit chapter(s). I believe that stuff should still be available as part of the Carbon documentation (yep, I just checked, it's still there). To complicate matters there are two forms of the style information - an internal form which consists of a bunch of linked structures, and an 'external' form which is used for style information on the clipboard and in resources. This is a     StScrpHandle, which is what a 'styl' resource is. It's a hideous, horrible gnarly thing with tables and offsets to tables with relatively little information stored anyway and getting it right without error is a lot of effort.

My advice is not to do this. All of the TextEdit code is seriously out of date and deprecated, and there are far better ways to accomplish what you want. No new application should be even thinking about resources, let alone 'styl' resources. You need to figure out a way to use an rtf file for your SLA, which can be written in one line, and read in one line, and displayed to the user in one line. Modern disk images should have a way to let you do it this way - there's no way they'd be using TextEdit now. --GC

----

At the risk of being beaten with spoons, GTResourceFork contains code that converts 'styl' resources (paired with 'text' or 'str ' resources) into NSAttributedString instances. -JonathanGrynspan

----

Does it convert both ways? I suspect not, but it's going from NSAttributedString ->'styl' that's requested here. --GC

----

So it is. That's also a trivial conversion. You can use the GTResourceFork code as a starting point and work backward. -JonathanGrynspan

----

I wouldn't call it trivial exactly, 'styl' resources are fairly nasty. It's certainly doable, if you're prepared to discard much of what makes rich text rich, but my point is, I think, that this is a bad approach to the stated problem. Doable doesn't mean should-doable ;-) --GC

----

It looks like there was a fairly easy solution:

If you use the NSPasteboard class, you can interchange between different data types using the declareTypes: and the dataForType: methods.  The three types necessary for interchanging TEXT styl and RTF data are:

    
@"CorePasteboardFlavorType 0x54455854"   // 'TEXT'

@"CorePasteboardFlavorType 0x7374796C"   // 'styl'

NSRTFPboardType                         //  RTF


With this in mind, it should be pretty easy to extract the NSData necessary from an NSAttributedString using RTFFromRange:documentAttributes:

Hope that helps anyone else in the future

- CorporateNewt

----
That is an ugly and yet extremely clever solution. Well done! -- MikeAsh

----
The only problem I'm having thus far is the conversion of non-english characters.  If I use the NSPasteboard functions to convert the TEXT and styl resources to RTF from a UDIF Software License Agreement with, let's say Traditional Chinese characters, they don't show up the way they're supposed to.

Anyone know a fix?
----
To preserve non-Roman characters, look for the utxt/ustl resources instead of TEXT/styl if available. These should be available if you're dealing with clipboard data from Carbon apps. User:Tempelorg|Tempelorg (User talk:Tempelorg|talk) 08:38, 11 January 2013 (EST)

