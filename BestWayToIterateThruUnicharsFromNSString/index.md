---
layout: page
title: BestWayToIterateThruUnicharsFromNSString
---



I know this has to be something very simple, and I'm sure I'm making it a lot harder than I need to.  I can't seem to find a site that says "Here's the way This Is Done in Cocoa", so here I go.

I'm reading a string from an NSTextField that comes back with a series of (for development ease's sake) 9 characters all numeric (so, let's say 123456789).

I would like to print out each unichar as I iterate through the constituent unichars.  Here was my first stab at the problem:

     
        NSString * testStringpre = [fastFillLine1 stringValue];
	NSMutableString * testString = [NSMutableString stringWithString:testStringpre];
	
	NSLog (@"length is : %d", [testString length]);
	
	if ( [testString length] != 9 ){
		NSLog(@"There were insufficient characters in passed-in line");
	}

	int yIterator;
	yIterator =1;

	while ( [testString length] > 0 ){
		unichar loadChar = [testString characterAtIndex:0];
		NSLog(@"Char is: %C", loadChar);
	
		[testString deleteCharactersInRange:NSMakeRange(0,1)];
		unichar passable[1];
		
	}


Is this a standard way of attackintg the problem?  Would it be superior if I were to make NSStrings from the range(0,1) instead?

----

Perhaps I'm missing the point of what you're trying to do with the character, but why wouldn't using an index work?

    
NSString *characters = [fastFillLine1 stringValue];
unichar character;
int index;

for ( index = 0; index < [characters length]; index++ )
{
	character = [characters characterAtIndex:index];
	
	// perform something here
}


Also, although it's not important, an easier way to create a mutable string is to use [string mutableCopy].

----

I suppose I simply thought that there might be an enumerator and or, because they go on for such a wearying length about it in the documentation that doing something as intuitively simple  as what you suggest would be not the right way.

Good Call, though.

----

Enumerators are for collections. While strings are technically collections of unichars, they're not normally treated like collections at all, and that's why you don't get an enumerator. (Unicode makes it insanely hard to properly iterate over a string's characters anyway, which is why other mechanisms like rangeOfString: and NSScanner are provided.) When you do need to iterate, and you know that Unicode isn't going to harm you, then a for loop is just fine. Could somebody (preferably the original poster) refactor this page away from MailingListMode and into something that's suitable for a Wiki, and also move it to a page with a better name? -- PrimeOperator

----

I'm glad to see somebody is picking up the Mailing List ball and running with it a little. I searched this site in vain for pages onto which to refactor this discussion using terms unichar NSString NSTextField     characterAtIndex iterate and several other terms. Because the site has been used so much in MailingListMode over the last year or so, many searches result in lists containing dozens to a hundred or more pages. This is a useful discussion, but it's not really "about" anything in particular, other than whether there is an optimal way to use the frameworks *in a particular case, a very particular case*. Is this about NSString? Not really. Enumerators? Not really. Unichar? Not really. It's just about getting a sense of how the frameworks can help solve an idiosyncratic micro-problem in getting data from the interface into a program's internal machinery. This is not a bad thing, and we cannot bear to throw it away once the OP has had his question answered. So the page remains. Hundreds and hundreds of pages just like this are part of CocoaDev today, and most will rarely be useful except as one-off Q-and-A's. Mailing lists shed these like old skin cells, and are never demonstrably the poorer for it. If no one else does anything, I think this page could be retitled as StringsAreNotCollections, and the relevant discussion moved there, sans my perennial ranting. It's a useful conversation, which specifically (because of your perspicacity) points out the notion that StringsAreNotCollections, and should not generally be treated as such. 

-- BoozeDog

----
Another idea, how about a more general page titled StringParsing? It could cover basic loops, as well as more complicated things like NSScanner and regex frameworks. I think that would help this information fit in better with everything else. What do you think? -- PrimeOperator

----

That's good, too, but I find it a little intimidating in its scope. How about as a stub? It would be good to have a base page here that fans out on all that jazz. I vote yes. The above issue would seem to be something of a footnote on all of that. I like the idea of dovetailing some of HowToConvert with it, too.

-- BoozeDog

----

I think you're right, it should be a topic page instead of a "put everything here" page. This could then go on that page, or some small separate page as a topic entry. -- PrimeOperator

