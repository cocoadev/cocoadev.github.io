---
layout: page
title: FindingIfOneStringContainsAnotherString
---

There seems to be no 'contains' operator in the NSString class.  This seems like a weird oversight.  So if I have a String that contains @"my IP is" and I want to see if that string contains the word "IP" somewhere in it how would I do this?  I looked at NSScanner but couldn't get that to work - the results were always null.

here's a real example:

(I want to see if members of an array contain the word rendezvous them so I am NSEnumerating through the array)

 NSScanner *scanner = [NSScanner scannerWithString:[allPreviousHosts objectAtIndex:x]];

        if (![scanner scanString:@"rendezvous" intoString:@"rendezvous"]){  do something }

this should tell me if the string set in scannerWithString contains "rendezvous" right?

what am I missing?
----
I believe the scanString:intoString: looks at the current scan location (or immediately after any characters set to be skipped) and so will only find "rendezvous" if it is the first word in the string. I would think using an NSString constant for intoString: would cause problems, since the method wants an NSString **, that it will try to set to the found string.

Regardless, what you could use is rangeOfString: on your NSString. This is also in StringWithinAString.

----

Turns out this was already answered in the link above:

the answer is:

if ([stringToBeScanned rangeOfString:theStringYouWant].location != NSNotFound) {blah blah}

but man, it would be SO MUCH EASIER if apple gave us a 'contains' function!!!

----

Well, NSString's -rangeOfString: is a pretty straightforward replacement.  It returns an NSRange with a location of NSNotFound if it doesn't contain the string, and if it returns anything else than it does. -- Bo

----

Here's what I always do:

**NSString+Additions.h**
    
@interface NSString (SearchingAdditions)

- (BOOL)containsString:(NSString *)aString;
- (BOOL)containsString:(NSString *)aString ignoringCase:(BOOL)flag;

@end


**NSString+Additions.m**
    
@implementation NSString (SearchingAdditions)

- (BOOL)containsString:(NSString *)aString
{
    return [self containsString:aString ignoringCase:NO];
}

- (BOOL)containsString:(NSString *)aString ignoringCase:(BOOL)flag
{
    unsigned mask = (flag ? NSCaseInsensitiveSearch : NULL);
    NSRange range = [self rangeOfString:aString options:mask];
    return (range.length > 0);
}

@end


Or you could just write a containsStringIgnoringCase: method.

----

Wait, with the example above the code you wrote is to be saved as additions.h?  and then you include it in your project?  Please - a little bit more info on how to implement this!

This is a catagory to NSString, so just include the files NSString+Additions.h/.m as designated above, and all NSStrings in that project will subsequently be able to respond to those messages.

----

works great!  merci!!!

cocoadev rocks!!

----

I know it's already been answered, but before I knew about these easier methods, I made two of my own. I think you guys will get a kick out of my newbie ignorance at the time:
    
- (BOOL)containsString:(NSString *)aString
{
    return (self mutableCopy] replaceOccurrencesOfString:aString withString:aString options:[[NSLiteralSearch range:NSMakeRange(0,[self length])]>0);
}


Yeah, it's a little hacky (and hideously inefficient!).
Next, another inefficient, but "think outside of the box" approach:
    
- (BOOL)containsString:(NSString *)aString
{
    return ([[self componentsSeparatedByString:aString] count]>0);
}

--Goldfish

----

Hehe. Definately "outside the box".

----

Well, that depends on which box you're in, doesn't it?
 Pour participer   maintiennent numéro, vous aurez  peut avoir  votre compte   Agent  ( Règle) [http://obtenir-rio.info rio bouygues]. Vous obtiendrez  pour  gratuit  par  téléphonant   mots  du serveur ou du service à la clientèle  clientèle   votre propre  vieille fournisseur  [http://obtenir-rio.info/rio-bouygues numero rio bouygues] . Vous ne   acquérir  un SMS  utilisant votre. Avec votre  [http://obtenir-rio.info/rio-orange code rio orange], alors  vous êtes capable d' à l' offre de  de  ON   citrons .

