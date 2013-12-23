---
layout: page
title: RegularExpressions
---

A regular expression is:

*  a string whose pattern describes a set of strings. 
* is a set of characters (string) that specify a pattern.

Also known as a regex or regexp.

[Topic]

----

References:


*  The re_format(7) man page. [http://developer.apple.com/documentation/Darwin/Reference/ManPages/man7/re_format.7.html]
*  Mastering Regular Expressions, 2nd Edition [http://www.oreilly.com/catalog/regex2/]
*  Regular Expressions: [http://www.grymoire.com/Unix/Regular.html]
*  GNU grep: [http://www.gnu.org/software/grep/doc/grep.html]
*  Regular Expression HOWTO [http://py-howto.sourceforge.net/regex/regex.html]
*  Five Habits for Successful Regular Expressions: [http://www.oreillynet.com/pub/a/onlamp/2003/08/21/regexp.html]
German version: [http://www.oreilly.de/catalog/regex2ger/index.html]
*  Regular expressions simplify pattern-matching code: [http://www.javaworld.com/javaworld/jw-02-2003/jw-0207-java101.html]
* International Components for Unicode [http://icu-project.org/userguide/regexp.html]


----

Software:


* NSRegularExpression is the ICU-based Regular Expression support provided in Foundation on OS X 10.7 and later, and in Foundation on iOS 4 and later.
* ObjPCRE ( http://sourceforge.net/projects/objpcre ) is a well documented PCRE wrapper written in 100% Objective-C/C. It can be easily implemented into any Cocoa application.  Does not appear to support extracting groups from a matched string. (MIT License)
*  AGRegEx ( http://sourceforge.net/projects/agkit ) is an OO wrapper for PCRE -- works great
*  MOKit's Regular expression:  http://www.lorax.com/FreeStuff/MOKit.html
*  OFRegularExpression (part of the OmniFoundation Kit)
*  OgreKit (OniGuruma Regular Expression Framework for Cocoa): http://www8.ocn.ne.jp/%7esonoisa/OgreKit/index.htm
*  PCRE, as used in many software packages, including PHP. [http://pcre.org/]
*  regex(3) - the regular expression library built into Darwin/Mac OS X. [http://developer.apple.com/documentation/Darwin/Reference/ManPages/man3/regex.3.html]
* CSRegex - Objective-C wrapper for the libc functions above -- simple and easy to include in projects.
*  REtry - showing use of MOKit [http://deallus.com/retry.shtml]
*  RegexKit - PCRE based regular expressions.  Excellent documentation [http://regexkit.sourceforge.net/]
*  CocoaOniguruma - a simple and tiny binding of Oniguruma


----

I have looked into a few RegularExpressions libraries for use in my program, but all the ones I have used are rather slow. I've resorted to my original way of piping data to a Perl script which handles the regular expressions and returns it back:
    
#!/usr/bin/perl
# script for removing HTML
$str = "";
while ($line=<STDIN>)
{
	$str .= $line;
}
$str =~ s/<(?:[^>'"]*|(['"]).*?)*>//gsi;
print $str;
If anyone knows of a **fast** way of doing regular expressions that is thread safe, please let me know!

*Maybe not as easy to work with, but you could use flex to generate the scanner (I _think_ the experimental C++ output is thread safe). There are tools similar to flex, i.e. which generate a DFA-based matcher, and thus are much faster than the misc. regexp libraries -- as for libraries, perl is probably the most optimized one!*

*P.S. To do the above (with perl) you can call (can't disable the italic, so view source):*
    
perl -pe 's/<(?:[^>'\*"]*|(['\*"]).*?)*>//gsi'


----

I don't know if it's any faster than what you've already tried, or even usable, but NSString has some undocumented regexp functions.

----
There is a NSStringRegExp addition:  http://homepage.mac.com/jrc/contrib/

----
Note that this addition is not unicode-safe; in particular, the returned ranges are incorrect for strings containing non-ASCII characters, which will probably result in thrown exceptions as the substring operations fail.

Another wrapper around the regexec from C is also available at: http://www.spikesoft.ch/?p=24

----
No need for an external solution to test a NSString object against a regexp!
Regular Expressions for NSString
http://www.stiefels.net/2007/01/24/regular-expressions-for-nsstring/

----
Unfortunately in many cases being able to test only for matching without being able to get the location or contents of match groups is as close to useless as makes no difference.

