---
layout: page
title: UniCode
---

*The increasing use of the Internet for global commerce renders the need to efficiently transmit,store, and manipulate multi-lingual data ever more urgent. It is no longer acceptable for companies wishing to do business on a worldwide basis to impose a single language (i.e. English) as a common denominator. They must be able to communicate with potential customers in the customer's native language. Unicode is the first character set that enables all the world's languages to be encoded in the same data set. Prior to the introduction of Unicode, if users wanted to store data in, for example, Chinese, they were required to choose a character set appropriate for that language - to the exclusion of most other languages. It was not possible (or in any case, highly impractical) to mix character sets, and thus diverse languages, in the same data set.* http://goo.gl/General/OeSCu

taken from -> http://www.sybase.com/content/1013476/4073UNI-chorwpaperv3.pdf

**Links**

* http://www.unicode.org/charts/charindex.html
* http://en.wikipedia.org/wiki/Unicode 
* http://en.wikipedia.org/wiki/UTF-16


----

It looks like Apple is trying to move further away from ASCII characters. File path encoding recommendations in General/CoreFoundation and Cocoa are now UTF-8. So what does this mean for those who like ASCII? Not much at the moment.     printf will be around for some time, but byte order character strings may be a thing of the past. 

UTF-16 is the native data format for General/NSString. This means that all characters in an General/NSString are multiples of 16 bits wide. Programmers that only deal with ASCII characters really don't have to worry about any of the implications of UTF-16 because ASCII characters cast to UTF-16 encoding. 

    
    char c = 'a';
    unichar uc = c;
    General/NSLog(@"char: <%c> unichar: <%C>", c, uc);

    Output:
        2004-11-21 10:52:12.670 test[4760] char: <a> unichar: <a>


This is all basic info. But, the main reason this topic was created was to talk about some of the neat characters you can use when characters are 16 bits wide (unichar).

For example the symbol for a giga byte is the unichar value of (0x3387). Most fonts will display this value nicely. There are many other nice symbols you can use in your GUI like arrows (0x2962 - 0x2965).

----
Actually, the GB symbol only exists in Japanese fonts.

----
To see most of them log this code snippet, but this could be slow if you have an old mac ;-)

    
	int i;
	General/NSMutableString *string = General/[NSMutableString string];
	for (i = 32; i < 55296; i++) {
		if (!(i % 10)) [string appendString:@"\n"];
		[string appendFormat:@"(%04X:%i)%C, ", i, i, i];
	}
	General/NSLog(string);


**Symbols**

*Option(0x2325), Delete(0x2326), Command(0x2318), Shift(0x21E7), Control(0x2303)
*Hz(0x3390), kHz(0x3391), General/MHz(0x3392), General/GHz(0x3393), General/THz(0x3394)
*KB(0x3385), MB(0x3386), GB(0x3387) (most SI units are between 0x3380 and 0x33D8)
*Circled letters (A-Z, a-z) are between (0x24B6 - 0x24E9)


--zootbobbalu

----

Also, here is a page describing the mapping between Mac OS Keyboard character set to Unicode 4.0 and later:

http://www.unicode.org/Public/MAPPINGS/VENDORS/APPLE/KEYBOARD.TXT

----

 I need to specify an Unicode character in General/NSString in my code, and I simply don't know how to do it. If I try with escape sequence \u or \U (for example: General/NSString *aString = @"p\u00c0pe";), which is the regular way stated in documentation on several places, I get warning from the compiler, complaining that '\u' or '\U' is unknown escape sequence, and it's simply ignored (aString actually gets the value of "pu00c0e"). Am I missing something, or what? Thanx in advance, Dragan 

----

The proper way to create General/NSString that contains unicode characters is to use stringWithUTF8String and the \u#### escape sequence, like this: 

    
General/NSString *aString = General/[NSString stringWithUTF8String:"To be continued\u2026"];


So no double backslashes are needed, and UTF8-encoded characters are used inside a plain C-string. Creating strings from separate characters (as shown later on this page) is too complicated and unnecessary for most purposes.

----
*Here is the answer, moved up from further down:*You have to use \\U (two backslashes - strange but true):

    General/NSString *aString = @"p\\u00c0pe";

the reason for that is the first backslash is an escape in the string literal (so you can do things like @"\""); the second backslash is the one that makes it into the string to be parsed by General/NSConstantString. *--boredzo*

----
I don't think you can specify unicode characters in a statically allocated General/NSString like that (one created with @"..."). Use     initWithUTF8String:(const char *)bytes or     initWithData:(General/NSData *)data encoding:(General/NSStringEncoding)encodingAlso see [http://www.mulle-kybernetik.com/weblog/archives/000519.html]

----
Note that embedding UTF-8 string constants in your code will work if your source file is UTF-8, but you're invoking the God of Undefined Behavior and your code could break in the future. The best way to get a unichar into a string is like so:

    
unichar ch = 0xbeef; // replace this with your code pointNSString
*str = General/[NSString stringWithCharacters:&ch length:1];


The best way to get multiple unichars into a string is to use Cocoa's localization system. Put the actual string in your     Localizable.strings file, and then use     General/NSLocalizedString to retrieve it.

----
Or you can do something like this (taken from a mailing list post by Clark Cox):

    
const unichar myCharacters[] = {0x03C3,'x','x'};
General/NSString *myString = General/[NSString stringWithCharacters: myCharacters
                                             length: sizeof myCharacters / sizeof *myCharacters];


----
Or you can do somthing like this:

    
const unichar UTF16String[] = {0x03C3, 'x', 0x2318, nil};
General/NSLog(@"sigma: <%C> asci 'x': <%C> Command Symbol: <%C> UTF16String: <%S>",
      0x03C3, 'x', 0x2318, UTF16String);


--zootbobbalu

----
Well, thank you guys, I could manage to do something using approaches you suggested (General/NSData, bytes and unichars...) but I wanted to know why '\u' escape sequence didn't work. This explanation with double backslashes says it all, although I find it not only strange, but ridiculous as well.

----

I think I know what's wrong with \u. Very likely the \u00c0pe escape is translated directly through the compiler. However, because the backslash is in a string, it would normally be taken out, NOT giving the desired effect. In Java, at least, you can turn on Unicode escapes throughout all your code (meaning you can put \u2026 anywhere in Java code to generate the character � (the ellipsis character). Apple *may* have implemented something like this for General/ObjC (I'm just guessing), in which case you WOULD need two backslashes, in order to keep the default C compiler from trying to parse \u. It's still not a great solution though -- Apple should have changed SOMETHING so this would work. --General/JediKnil

----

no they shouldn't. the compiler is supposed to parse all backslash sequences it encounters - even inside strings. that is why     @"foo\tbar" becomes      { 'f', 'o', 'o', 0x09, 'b', 'a', 'r' }  - because the compiler expanded the backslash sequence in the string.when the compiler encounters a backslash sequence it doesn't recognise in a string, it leaves the escaped character intact. thus,     @"foo\u0009bar" becomes     @"foou0009bar". General/NSConstantString never sees the backslash because there isn't one in the string. and the compiler itself does not support \u sequences.this is why you need to escape the backslash - so that it winds up in the string, so that General/NSConstantString can see it and thus expand it.*--boredzo*

I recant my previous statement. using \\u or \\U will put a u or U into your string. the correct notation *is* \u or \U with only one \.every combination I've tried gives me either a warning (and a & on output) or an error, though. I guess gcc doesn't support this correctly yet.*--boredzo*

----
FWIW, I have put isolated Unicode characters directly into source files, and saved them as UTF-8, though I haven't tested this thoroughly at all.  Also, although it's clunky, you can do something like this:

    
General/[NSString stringWithFormat:@"%C",0x03C1]


 if you need only a few characters.*--BCE*

----

Putting raw UTF-8 into C string constants works in gcc, but is not documented to work anywhere, so you're relying on undefined behavior. Don't do this. You can embed UTF-8 without relying on undefined behavior by using escapes, and that is guaranteed to work.

----
Its not documented, but I use it for generating strings for unit-testing, where its quite safe :-)

I use this macro:
    
#define U(s) General/[NSString stringWithUTF8String:s]


Then I can write things like this:
    
General/NSString *test = U("hello - ����");


Which is almost nice as Python unicode strings:
    
u"hello - ����"


----

No you can't, because there's no guarantee that your C string constants will be interpreted as UTF8. If they're interpreted in another character set (e.g. General/MacRoman) they could be modified. If you need Unicode text, better to put it in Localizable.strings (which is interpreted as UTF8 by the appropriate authorities) and use General/NSLocalizedString(@"FOOBAR", nil) to fetch it.

----

In other words, **PLEASE** always remember that you cannot put any characters other than ASCII < 127 in a source file with any sort of predictable results! No amount of macro trickery will avoid this downside of working with gcc.  --K
