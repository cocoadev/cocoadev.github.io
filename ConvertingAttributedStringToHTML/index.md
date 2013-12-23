---
layout: page
title: ConvertingAttributedStringToHTML
---



I have an NSAttributedString with formatted text (different fonts, colors, styles, etc.) as well as with images. Is there any way out there that will take this string and turn it into HTML without me having to spend the three or four days writing and perfecting a parsing engine that will probably end up being slower than what it http://goo.gl/OeSCu desirable? I don't see anything for this in the class itself, so perhaps someone can point me to a third-party framework or technique to help me out here?

TIA,
MarcWeil

----

You're best bet it to save it as a .rtf file and then use a generic library to convert it to html.  Here's a promising one: http://www.gnu.org/directory/webauth/htmlconvert/rtf2html.html  You should be able to find more on google.  If you get this working with images (a lot more difficult) please share.

-- SaileshAgrawal

----

And you're sure there is nothing else out there that can parse images? I'm actually quite surprised taht the EDFrameworks don't cover this. They seem to cover just about everything else (except good documentation, but that's another issue entirely ;-) ). I found another parser out there that claimed to be able to handle images, but instead of placing an IMG SRC tag for the image, it just placed the images name. Seems like a pretty easy way out of the problem for that developer.

-- MarcWeil

----

As an update, I found this program called rtfdtohtml which is a Perl program that converts NEXTStep rtfd files (OS X uses the *exact* same format for its .rtfd files), images (either embedded or linked like OS X does it), and all into an HTML file.

However, the problem is that this program is almost like an addon or a patch for the program rtftohtml. This is where the problem is. I can't seem to find rtftohtml anywhere out there. Any references to it point me to www.logictran.com which has some commercial product called rt2net or something along those lines. This of course, being commerical and not being rtftohtml, will not work at all.

Does anybody know where I can possibly find a copy of rtftohtml? The rtfdtohtml docs say that at least version 2.7.5 is needed for it to work. rtfdtohtml's website points to an FTP server that supposedly has rtftohtml on it, but I can't seem to log into it to see if it is really there. It doesn't seem to accept anonymous or guest logins.

Any help please??

-- MarcWeil

----

Wait, why not just -[NSAttributedString initWithHTML:documentAttributes:] ?

If you need to be able to tweak the parsing code, there's some code in Adium 2 that does that (copy and paste these URLs, don't click on them- the wiki engine isn't noticing the part after the &):

http://cvs.sourceforge.net/cgi-bin/viewcvs.cgi/*checkout*/adium/adium/Frameworks/AIUtilities%20Framework/AIHTMLDecoder.m?rev=HEAD&content-type=text/plain
http://cvs.sourceforge.net/cgi-bin/viewcvs.cgi/*checkout*/adium/adium/Frameworks/AIUtilities%20Framework/AIHTMLDecoder.h?rev=HEAD&content-type=text/plain

(It's GPLed Free Software, though, so if you use it, you'd have to GPL your program also.)

-- AdamAtlas

----

Well, the idea here is to start with an NSAttributedString and convert that to HTML, not the other way around. The user will have a text box where they can enter styled text, insert pictures, etc. Naturally, the NSTextView will store its contents as an NSAttributedString. But, this styled data must be converted into HTML so other things can be done with it. There is a short tutorial in "Cocoa Programming" by Scott Anguish, et. al. on how to do this with just underlined formatting, but I cannot figure out how to make it work with bold, italic, underline, different fonts, colors, different sizes, and images all in the same NSAttributedString.

As SaileshAgrawal suggested above, it would probably be easier to utilize NSAttributedString's method of saving itself as either an RTF or an RTFD file, and then converting THAT with a third-party application or library. Sounds easy enough, except I can't get any of the things out there to do this because either a) they are only for RTF and don't support images, or b) they ARE for rtfd, but they will only work on NexTStep-compatible computers, which, as much as Apple would like to think, OS X is not.

Besides, I need to sell this piece of software, so using code from a GPL application isn't an option. :-) Thanks for the help though, Adam.

-- MarcWeil

----

What about WebKit? -- ColinBarrett

----

ChatCore has a NSAttributedString additions category to do just this:

http://cvs.sourceforge.net/viewcvs.py/colloquy/colloquy/NSAttributedStringAdditions.m?rev=1.15&view=auto

Fire now has a modified version that exports HTML with more modern CSS. (Colloquy has it's reasons for being old school.)

http://cvs.sourceforge.net/viewcvs.py/fire/fire/NSAttributedStringAdditions.m?rev=1.42&view=auto

~ TimothyHatcher

----

When you have a newline (new paragraph) and the paragraph style changes it causes there to be two "invalidate paragraphs" and thus causes it to insert one empty paragraph... causing it to appear like it has double spacing (two paragraphs). You can test this by typing stuff with more then one paragraph (with newlines) and then changing the text alignment a few times.

Here is the category (and hopefully all of the support it needs):

    
@implementation NSObject (TRFoundationAdditions)
+ object
{
	return [self alloc] init] autorelease];
}
@end


    
@implementation [[NSMutableString (TRFoundationAdditions)
- (unsigned int)replaceOccurrencesOfString:(NSString *)target withString:(NSString *)replacement
{
	return [self replaceOccurrencesOfString:target
								 withString:replacement
									options:nil
									  range:NSMakeRange(0,[self length])];
}
@end

