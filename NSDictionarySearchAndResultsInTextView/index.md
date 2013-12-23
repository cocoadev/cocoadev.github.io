---
layout: page
title: NSDictionarySearchAndResultsInTextView
---

Hi-

I have a dictionary that I'm searching.  The dictionary has a key (NAME) with 2 corresponding objectsforkey (PHONENUMBER and EMAILADDRESS).

I'm trying the put the results of my search into 3 different TextViews, one for each of the above items (name, phonenumber and emailaddress).

I seem to keep running into a snag when I try this:
    
- (IBAction)search:(id)sender
{
NSEnumerator *myEnumerator;
id placeholder1;
id placeholder2;
id placeholder3;
NSDictionary *dictionary;
NSString *name;
NSRange range;
	
// search my plist via an nsdictionary

myEnumerator = [plist objectEnumerator];
placeholder1 = [[[NSMutableString alloc] init] autorelease];
placeholder2 = [[[NSMutableString alloc] init] autorelease];
placeholder3 = [[[NSMutableString alloc] init] autorelease];
	
while (dictionary = [myEnumerator nextObject]){
name = [dictionary objectForKey:NAME];
range = [name rangeOfString:[searchField stringValue] options:NSCaseInsensitiveSearch];
if (range.location != NSNotFound)  {
			
[placeholder1 [NSString stringWithFormat: name]];
[placeholder2 [NSString stringWithFormat: [dictionary objectForKey:PHONENUMBER]]];
		
[placeholder3 [NSString stringWithFormat: [dictionary objectForKey:EMAILADDRESS]]];
		
		}

	}
		
	// do this if nothing was found in the search

if ([placeholder1 length] == 0)
placeholder1 = @"";
placeholder2 = @"Not Listed";
placeholder3 = @"";		
		
	//  otherwise put the found key's string and its corresponding objectforkeys in their own respective textviews 
		
[textview1 setString:placeholder1];		
[textview2 setString:placeholder2];
[textview3 setString:placeholder3];
	
}


I know I'm missing something (or some things) basic here, but I'm not sure what. 

----

About a dozen or so lines up it looks as if you want to assign some strings to your placeholder strings for phone and email address

I'm confused by the syntax which looks as if you are trying to treat the strings returned from
[ NSString stringWithFormat: [ dictionary... etc ] ]
as MESSAGES to your placeholder string objects.  -- I bet the compiler gives you a more cryptic complaint  ;-)

I'm not an expert, but I can't say I understand how an OBJECT can be used as a MESSAGE. 
I've just never seen it done. I'll suggest that what you mean to do is for example

placeholder2 = [ NSString stringWithFormat: @"(some  format string is missing here)",  [ dictionary objectForKey:PHONENUMBER ] ];

----

Yup, you were right.  Thanks alot.

