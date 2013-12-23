---
layout: page
title: IgnoreAccents
---



*Note: This discussion contains accented characters which seem to be best viewed under the "Western (ISO Latin 1)" text encoding.*

When I compare two UTF-8 encoded strings, I want the accented characters be seen as regular characters.
For example, if "�" is found in a string, it will act like "e" and "�" and so on.
Another example : "parle" and "parl�" will be considered the same when compared.

----

How are you comparing the strings? NSString's     compare:options:range: should do the right thing as long as you don't specify NSLiteralSearch
----
I'm doing this, through the method     sortUsingSelector:@selector(compare:) I can't use anything else since I don't know if you can pass parameters within this method.

Here's a real-life example :

    
NSString *mot1 = @"arc";
NSString *mot2 = @"a";
NSString *mot3 = @"�";
NSMutableArray *sim = [NSMutableArray arrayWithObjects:mot1,mot2,mot3,nil];
NSLog(@"%@",sim);
NSLog(@"---");
[sim sortUsingSelector:@selector(compare:)];
NSLog(@"%@",sim);


The logs show me what happened. "�" is supposed to be equivalent to "a", not after, not before.

So the correct sorting should be, in ascending order : "a","�","arc".
But the computer sort of thinks "�" as the second letter of the alphabet. So I get the wrong result : "a","arc","�".

----

Just add a new selector to the class:

    
@interface NSMutableArray (MyAdditions)

- (int)ignoreAccentsCompare:(NSString *)aString;

@end

@implementation NSMutableArray (MyAdditions)

- (int)ignoreAccentsCompare:(NSString *)aString
{
 return [self compare: ... options: ... range: ...]; // 3rd party edit: changed super to self
}

@end


-FranciscoTolmasky

----

All searches are carried out as if they were NSLiteralSearch for now.  When the use of UTF is more widely spread, apple plans to switch to non-literal search.  This is in the documentations under Strings-->Searching and Comparing Strings.  file:///Developer/ADC%20Reference%20Library/documentation/Cocoa/Conceptual/Strings/Tasks/SearchingStrings.html

----

I don't think that's a solution Francisco, there really is something strange going on here.  

Each of the following statements evaluates to true.

    
@"�" compare:@"a" == NSOrderedSame; // so "�" <= "a"

@"a" compare:@"arc" == NSOrderedAscending; // so "a" <= "arc"

@"�" compare:@"arc" == NSOrderedDescending; // but "�" is not <= "arc"


<= should be transitive, shouldn't it?  I'd call this a bug.  I tried a couple of options with -[NSString compare:options:], but the problem is still there.

----

You cannot embed non-ASCII characters in an NSString constant string, so @"�" is not turning out to be anything like you expect. See UnicodeCharactersHowTo for how to do this. Your program isn't breaking because of accents, it's breaking because @"�" is actually two or three completely unrelated characters.

*I'm sorry I transcribed it like that, but this is not the issue.  I actually tested it in FScript, which does let you enter unicode characters easily.  Since then I've tried it in a test project using a strings file.  If you doubt that this is a real phenomenon, please try it.*

What options did you try? If you don't specifically **not** specify NSLiteralSearch, you'll get a literal search. Try just putting     0 or     NULL for the options.

*
What?  Of course I tried 0.  Why would I type in NSLiteralSearch if I didn't want it?  Wouldn't that be kind of obvious?  I tried a few options just for kicks, but do you realize that (1) there are only three mask values to play with, (2) we obviously don't want any of them except maybe for     NSCaseInsensitiveSearch?  I tried 0,     NSLiteralSearch,     NSCaseInsensitiveSearch,      NSLiteralSearch | NSCaseInsensitiveSearch, and     NSNumericSearch.
*

----
I tried with localizedCompare: and also with nil options. The same result occured.
Could it be that the original encoding of my text file (the file used to create my array of strings) is not appropriate ? If all else fails, does it mean I'd have to ignore accents manually ? By manually, I mean : take the string, break it down in single characters, check if it has accent, if yes set it to its corresponding non-accented letter, then rebuild the string and return the result. That's a lot of manipulation...

*If you actually tell us what encoding the text file uses, and how you're reading it, we might be able to say.*
----
To be frank, I don't know exactly. There's no BOM at the beginning of the file, so my best guess would be high-order ASCII. Anyway, the docs say that NSString always uses UTF-8. Besides, I don't see how NSLiteralSearch could be the problem.

*Well, how did you create it? How are you reading it? Throw us a bone, here....*

----

I don't think this has anything to do with an encoding mismatch, since I (not the OP) verified the problem using a .strings file, UTF-16 encoding as usual.  Anyone who wants to help here should check that what they are saying makes some sort of sense by trying out their suggestions first.  Time to create a test project:  1.5 minutes maybe?  My feeling is that this is a genuine cocoa bug.

To the OP:  If this is to be a localized sort anyway (note: other localities disagree on what alphabetical means.  In swedish an 'a' with a circle on top is a distinct letter and comes after 'z'), then try the carbon routine UCCompareTextDefault.  There's some sample code at http://www.cocoabuilder.com/archive/message/2002/5/22/62842 .  This came up on Apple's cocoa-dev recently, and it was observed that there's a little bug in that code because the "length" arguments are supposed to be the number of unichars in the array, and the sample code discounts the space taken by the BOM.  As a result the sample code always ignores the last character in each string.  I just tried this array sort using my local version of the code (and English locale), and it sorted the array into {'a', '�', 'arc'}.

This time around, the cocoa-dev mailing list people, including an Apple employee, concluded that you might as well use     -[NSString localizedCompare:], but earlier discussion (and my own experience) suggests that localizedCompare:, and I hate to say it like this, doesn't work.  I've never found a case where localizedCompare: and compare: gave different results.  Try setting your preferences to swedish and trying the a-with-circle to z comparison, for example.

----

Do not use UCCompareTextDefault, or use with caution, it has bugs. For example if you pass it an UTF NSString, it will not ignore the BOM character, so all sorts will come out NSOrderedSame.

*It works fine, it's only a bug in how you're calling it.  The three paragraphs above explain.  (I know they're hard to read.)  Also, even calling it wrong, if you're getting NSOrderedSame always it's because your strings only have one character.*

----

My array of strings is created like this :

    
dict = [[NSArray alloc] initWithArray:[[NSString stringWithContentsOfFile:fich] componentsSeparatedByString:@"\r"]];


In the process (I don't want to explain my entire prog), another array is created with strings taken from that big array, and then sorted. I tried with a little array (3 element, see top of the page for code snippet), and I get it wrong. My case is French, and I think it's not like Swedish (like �). In French, accented letters are equivalent to their non-accented counterparts, but IF a word is an homophone except for its accented letter(s), then it comes after. Here's another example I tried within my app and still don't work :

*mur
*m�r
*muse

This is correct alphabetical order. BUT,     sortUsingSelector:@selector(compare:) gives me this :

*mur
*muse
*m�r

Since "u" is supposed to be like "�", "s" comes after "r", but not in this case. Same with     sortUsingSelector:@selector(localizedCompare:)

----

Have you tried the code given in the link above?  That should work.

----

    -[NSString localizedCompare:]  actually works in Tiger!  Praise be!

----

You should also be able to use NSDiacriticInsensitiveSearch under Leopard. --Wevah

----

Under 10.4 the following constant is available for use with CFMutableString's CFStringTransform:
    
const CFStringRef kCFStringTransformStripCombiningMarks
The identifier of a transform to strip combining marks (accents or diacritics).

