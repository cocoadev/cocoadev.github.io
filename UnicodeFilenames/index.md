---
layout: page
title: UnicodeFilenames
---

I'm working with an existing C++ library which I would like to extend to support unicode filenames.  In Cocoa, I can just use General/NSFileHandle, and everything is happy.  But from C++, I'm stuck with open or fopen, which only accept "char *".  Is there a low level API somewhere that accepts "unichar  *" or "wchar_t *"?

- General/WendellHicken

----

I believe on OS X you can simply pass UTF8 encoded paths to open() and fopen().  -- Bo

----

That would be spiffy - I'll check and post my findings.  Is there a simple method to convert wchar_t * to UTF8? (Aside from General/NSString)
----

See **wcstombs** and friends.

----

Follow up: indeed, all the file methods seem to support UTF8 - life is good.  Many thanks!

----

Ok, related problem on *inputs* from system calls.  I have a file called Ma�ana.mp3.  When I receive this filename from opendir, it looks like this
(the character sequence, that is):

4d 61 6e cc 83 61 6e 61 2e 6d 70 33   (in other words, the � is represented by a three byte sequence: 6e cc 83)

From terminal, it looks like this if I use tab completion: Man\314\203ana.mp3 (which seems consistent)

As far as I know, this is not UTF-8 (the usual decode methods at least seem not to work).  If I put Ma�ana into a text file and save it as
UTF-8, the related sequence is "c3 b1".  (And in UTF-16, the single short is f1, if that's relevant).  Does anyone know how to convert
this name from opendir into UTF-16? (Edit: this is decomposed UTF-8, or decomposed UTF-16 - so it looks like more distinctions
need to be made when talking about this stuff.)

Attached is my test program for opendir, in case my error is there:
    
#include <dirent.h>
#include <stdio.h>

int main(int argc, char **argv) {
    DIR* dp;
    dp = opendir(argv[1]);
    if (dp == 0) 
        return -1;
    struct dirent* ep;
    ep = readdir(dp);
    if (ep == 0) 
        return -1;
    while (1) {
	unsigned char *name = (unsigned char *) ep->d_name;
	printf("%s  ", name);
	for (int i = 0; name[i]; ++i) {
	    printf("%02x ", name[i]);
	}
	printf("\n");
        if ((ep = readdir(dp)) == 0) 
            break;
    }
    closedir(dp);
    return 0;
}


----

More information on the file system encoding can be found at this tech note:

http://developer.apple.com/technotes/tn/tn1150.html#General/UnicodeSubtleties

Unraveling the techniques to convert this to "normal" unicode is still beyond me...

*It's using "normal" unicode, but with certain restrictions. However, as of 10.3, these restrictions are irrelevant to you, the application programmer. The strings coming back from system calls will always be valid UTF-8, and system calls will always accept any valid UTF-8 string. (It was supposed to be this way before, but there were certain problems with valid UTF-8 strings that weren't precisely how the filesystem wanted them.) There are many characters that can be represented in more than one way, but the system will correctly recognize them as being the same.*

----

Hmm, Coming from a BSD/posix call, I would assume (maybe wrongly) that this is multi-byte, what happens when you run it through **mbstowcs** to convert it to unicode?

----

It's multibyte alright, but not the correct kind to be decoded by mbstowcs (at least not with any locale I know of).  If you do so, you get an incorrect unicode string (i.e. one that cannot be used to access the file and does not display correctly).

Apparently the issue is the particular encoding used.  Even if you use Cocoa methods, and use an General/NSString to get the filename, you will
see that the file's name is in an unusual encoding (there are two unicode characters representing the �, which shouldn't be necessary
with unichar's).

*I'm not familiar with mbstowcs, can it handle General/UTf-8? If not, that would be why it doesn't work. The � character can be represented as composed (all in one character) or decomposed (an n followed by a ~ accent mark). HFS+ stores these characters as decomposed, but both ways are correct unicode.*

----

Apparently not the decomposed form.  I ran across another technical note which describes how to "precompose" the decomposed strings,
so this will be my next avenue of attack:

http://developer.apple.com/qa/qa2001/qa1235.html

The root of my problem is that I'm using an existing code based which represents filenames as STL wstring objects (32 bit characters),
so I need to be able to get into and out of those successfully with HTF+ filenames.

*Have you tried simply passing UTF-8? It should work, precomposed or not.*

----

Ok, the code is working.  Apparently the legacy code was doing a bad conversion from wstring to UTF-8.  I was able to get things working by using mbstowcs to convert
UTF-8 strings in wchar_t*, and wcstombs to convert backwards.  Whether the wstring itself is correct, I cannot say, since it's always converted
to something else before being used, but at least the results are consistent.

----

**Filename string from the iTunes library compared with that from the file system**

I am doing a comparison between two strings that contain slightly different values, they are filenames. I generate the first one from the iTunes library, the second one comes from the disk it gets written to (the filename), but for some reason they are not equal... the accented E becomes a different value in the two strings!

What do I do? :-/

The strings:

    
/MUSIC/T�l�popmusik - Love's Almighty.MP3
/MUSIC/T�l�popmusik - Love's Almighty.MP3


----

Ah, got it!

[fileMan stringWithFileSystemRepresentation:[newFileName fileSystemRepresentation] length:strlen([newFileName fileSystemRepresentation])]

Ie. converting it to a fileSystemRepresentation and back does the job!

*Just as a note, the two strings do look identical under Safari 1.2 and UTF-8 -- no accent. And there should be a solution that's a lot easier and cleaner -- see General/IgnoreAccents.*

----

At a guess, you're running into problems with precomposed versus decomposed characters in the unicode string. (Very briefly; the e' character can be a single unichar, or it can be the "e" unichar followed by a special accent unichar. They look the same but have different underlying representations.)

There are four General/NSString methods for converting to a consistent unicode representation, starting with     -decomposedStringWithCanonicalMapping in the header. If you run your strings through those first, they should compare to be equal.

----

How does Mac OS X know the difference between an ASCII file and a UTF-8 file when both end in .txt?

Create one of each using General/TextEdit, each containing this:

  �hello� fort�

In General/HexFiend you can clearly see the difference between the way they are encoded, but OS X somehow knows that one is ASCII, and one is UTF-8.

If you copy/paste the hex data from the UTF-8 file to the ASCII file, then open the ASCII file, OS X doesn't know it's supposed to be UTF-8, and interprets it as ASCII, like this:

  ���hello��� fort&#8730;�

It seems there must be some hidden property telling OS X whether the .txt file is ASCII or UTF-8 -- but what is it?

Thanks -- General/DarelRex@gmail.com

----
Not sure why you think this is "OS X" doing it, when you're using General/TextEdit for everything....

Note that your example file cannot possibly by ASCII. That accented E is not a valid ASCII character.

The encoding is stored in an extended attribute. See the section titled "General/NSString plain text file encoding support" in the Leopard Foundation release notes:

http://developer.apple.com/releasenotes/Cocoa/Foundation.html

----
Found it:  getxattr can be used to tell whether the file is UTF-8 or not.  Thanks for pointing me to research "extended attribute" -- that was helpful.

-- General/DarelRex@gmail.com

----
Care to elaborate - I've always read files names at UTF-8, are you saying there is an xattr which specifies an encoding of the files name? Please provide sample code - I'm feeling confused...

----
This latest discussion is somewhat misplaced. The xattr specifies the encoding of the *contents* of a text file, not its name. Names are always UTF-8 on OS X.
