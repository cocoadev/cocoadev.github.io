---
layout: page
title: CharacterEncoding
---

Character Encoding 101 starts NOW.

0) A character is an abstract idea.  It has nothing to do with what you see on the screen.  The letter D is the letter D no matter if its block text or drawn like the Detroit Tigers do on their ball caps.

1) A character SET is a set of characters (duh).  The set defines the entire alphabet, has nothing to do with computers, and is a completely abstract entity defined as a set of abstract entities called characters.  { A B C Z & ^ <  } is a set of characters.  The english alphabet is a character set.  The hebrew alphabet is a character set. The ,.?;:!- characters make up the set we commonly refer to as punctuation.

2) A character encoding is description of a representation of a character set in a computer.  If I say A=4, B=8, C=3.14, Z=-29, &=44, ^=13, <=-29000.  Thats a sort of encoding.  Its illogical and inconvenient but it might work for some application.  The term ASCII, you so blithely toss out is both a character set (letters a-z, A-Z, number 0-9, and the punctuation found on your keyboard, plus the null character) plus an encoding (a=93 for instance - see it all at http://www.asciitable.com/ ).

3) One additional detail is how a character's number will be represented on the machine.  The convention for ASCII is to use one byte per character.  But you can only represent 256 distinct values in a byte.  Chinese has well over a thousand characters.  So maybe each character takes up 2 bytes.

4) As the US computer companies slowly corrupted the rest of the world with the babel of computers, they shipped computers that had vendor defined encodings that typically had ASCII as a base and then used numbers greater than 127 (ASCII is representable in 7 bits) for the other "strange characters".  This worked until they discovered Asia.

5) Networks arrived and people began trying to exchange data - but the number 214 might meant one character to a Polish guy and something completely different to a Brazillian.  Characters are drawn using fonts - shape tables - pick the right shape table and 93 gets drawn as an 'a'.  Pick dingbats and its some weird bugsplat.  Basically, the Brazillian guy's text looks like dingbats to the Polish guy because their shape tables don't agree.  Translation gets crazy.  Notice that NSString supports something like 20 different encodings.

6) Enter Unicode - the Borg of character encodings, it absorbs all the existing character encodings into a huge number space.  Over 65 thousand at last count.  For extra credit, search the unicode standard for the second representation of ascii - its in there twice.  Note that this is just an encoding, there are several different machine representations for "unicode".  The smallest fixed sized encoding that covers all the characters in unicode is UCS-4 (4 bytes per character).  Holy cow!  If you're an american trying to save some space, you might be tempted to say BLOAT and to hell with Unicode.  Hold on though.  Unless you're doing something with ancient Sumerian or some other exotic character set, you can do pretty much all of the world's living languages in UCS-2 (2 bytes per character). These are the fixed length encodings.  There is also a variable length encoding called UTF8.  UTF8 uses 1 byte per character for ascii, 2 bytes per character for most european languages, 3 bytes per character for the asian characters (these are approximations).  Its size efficient but the drawback is you can only tell the index of the current character by reading from the beginning and counting to your current location.  A fixed width character set allows you to calculate your index in constant time but takes more space (sometimes).

NSString is *conceptually* a unicode string.  Internally it does everything in unicode.  It only gets messy when you read and write the string to some device (like a file or network).  Suddenly, exactly what format the bytes are in matters.  Thats why you have to specify an encoding when you go to NSData.  Most of these encodings are now considered legacy.  The world is going to unicode (nevermind that unicode sucks for asians - its the best thing going for now).  The preferred persistent format is UTF8.

So what encoding to pick?  If you're reading an older file from outside america, you need to find out what encoding it is.  If you know the language you only have a couple guesses to try - either its one of the legacy encodings or its unicode.  A couple heuristics will let you guess.

If you are picking the encoding, use UTF8 always - even if you know in your heart its ascii, use UTF8.  Why?  ASCII is a strict subset of UTF8 (except for Java's UTF8 stupidity but that's another story) and everything will just work.

{originally posted on Apple's CocoaDev list by Todd Blanchard}

posted here by DiggoryLaycock - because it explains so well - where Apple's docs just throw jargon at you

----
I would like to add just small addition:
There are some predefined constants in NSString.h like NSASCIIStringEncoding and such but they of course doesn't contain all available encodings on the OS X. I'm finding extremely usefull following code (for everybody who need to handle with text encodings)...
     
@implementation NSString(_encodings_)
- (NSArray*)allAvailableEncodings
{
    NSMutableArray*     array = [[NSMutableArray array] retain];
    const NSStringEncoding*     encoding = [NSString availableStringEncodings];

    while (*encoding) {
	NSMutableArray*	row = [NSMutableArray arrayWithCapacity:2];

        [row addObject:[NSString localizedNameOfStringEncoding:*encoding]];
        [row addObject:[NSNumber numberWithInt:*encoding]];
        encoding++;
	
	[array addObject:row];

    }

    return [array autorelease];
}

- (int)numberFromLocalizedStringEncodingName:(NSString*)aName
{
    NSArray *encodings = self allAvailableEncodings] retain];
    [[NSEnumerator *en = [encodings objectEnumerator];
    NSArray *encPair = [NSArray array];
    int searchedNumber = 0;
    
    while (encPair = [en nextObject])
    {
	if (encPair objectAtIndex:0] isEqualTo:aName])
	    searchedNumber = [[encPair objectAtIndex:1] intValue];
    }

    [encodings release];
    return searchedNumber;
}
@end
 
It simply asks OS X for all available encodings on the machine and returns the number based on localized name. The purpose is simple: For example, initWithData:encoding: method wants an *int* as an argument and its really extrem to supply something like: -27784221.
--[[BobC

----
Just found a link that also belongs here on the TextFileParsing page of CocoaDev... read it...loved it:
http://www.joelonsoftware.com/articles/Unicode.html --CharlesParnot

----
May be a bit outdated, but nevertheless quite interesting when comparing Carbon-Cocoa/Objc: How to detect string encoding? on http://www.macosxguru.net/article.php?story=20030808081801868 

----

For the record (some people don't get this and it is important):

From the Unicode.org FAQ: "Unicode characters may be encoded at any code point from U+0000 to U+10FFFF. The size of the code unit used for expressing those code points may be 8 bits (for UTF-8), 16 bits (for UTF-16), or 32 bits (for UTF-32)."

So, basically, it doesn't matter what Unicode encoding you use (UTF-8,UTF-16,UTF-32) you can always encode any character. Bring on the ancient Sumerian!

Further reading: http://www.unicode.org/faq/utf_bom.html
In particular: http://www.unicode.org/faq/utf_bom.html#37

----
To convert SHIFT-JIS  to UTF-8:  

Mac character code converter (mccc)

http://www.yansite.net/softparts/

http://www.popjisyo.com/WebHint/Portal_e.aspx  (basic translation help for Japanese <-> English )

----
And don't forget to create UTF8 strings with precomposedStringWithCanonicalMapping (cf. NSString; Unicode Normalization Form C, http://diveintomark.org/archives/2004/07/06/nfc )

----

Leopard (10.5) supports specifying the encoding of a text file via the "com.apple.TextEncoding" file attribute. This attribute has a format like "UTF-8;134217984", where the first part is the IANA name and the second is the CFStringEncoding value. NSString supports this to automatically read/write the encoding of a file.

