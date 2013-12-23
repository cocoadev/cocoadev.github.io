---
layout: page
title: ParsingTabbedNumericalData
---



I have a model class with exactly five NSNumber keys plus an optional label key for a row of data, to be displayed in a table.

A method in my window controller parses tab-separated text data line-by-line into arrays of strings and passes each array to
an initializer for the model class. It is possible that the label (non-numeric data) may appear anywhere in a tabbed line
and have to assume for sanity's sake that the label should never parse as numeric data. I even assume that more than one field
that can be parsed as a label may appear in the raw data, and use the last one encountered as the label for the row.
The row of raw data may contain fewer than five tabbed fields...**in other words, the row may have holes in it in any column.**

**My main difficulty is to try to preserve columns intact, rather than rows. And I don't know in advance where the holes will be.**

I have a reasonably successful preliminary implementation, but am willing to learn if anyone wants to suggest a better way.
I have only debugged this with a limited number of test data files. Any errors in the code are my responsibility. Or yours,
should you decide to use it.

My implementation for the initializer of the model class is as follows:

    
- ( id ) initWithStrings: ( NSArray * ) strings
{
	NSEnumerator *se = [ strings objectEnumerator ];
	NSString *item;
	char c;
	BOOL isaNumber = TRUE;
	NSMutableArray *nums = [ NSMutableArray arrayWithCapacity: [ strings count ] ];    // It's NOT a rectangular matrix.
	
	if ( self = [ super init ] )
	{
		while ( item = [ se nextObject ] )
		{
			int i = 0;
			//const char *utf8rep = [ [ strings objectAtIndex: 0 ] UTF8String ];    // wrong, wrong, wrong in view of my intent
                     const char *utf8rep = [ item UTF8String ];     // ALL the strings must be tested for being numeric or not, or even blank
			
			while ( utf8rep[ i ] )
			{
				c = utf8rep[ i++ ];
				if ( !isdigit( c ) && c != '-' && c != '.' && c!= '+' )     // determine if each string will parse to a float
				{                                                            // I should probably look for exponential-formatted numbers as well
					isaNumber = FALSE;
					break;
				}
			}
			
			if ( isaNumber )
			{
                             // 0 length implies an empty field within the row of data that will otherwise be parsed to 0.0
				if ( [ item length ] == 0 )
					//[ nums addObject: [ NSNumber numberWithFloat: 1.0/0.0 ] ];   // NSNumber will parse empty value to 0.0
                                  [ nums addObject: [ NSNumber numberWithFloat: -999.999 ] ];     // I need some marker to indicate missing data
				else
					[ nums addObject: [ NSNumber numberWithFloat: [ item floatValue ] ] ];
 			}
			else
				label = [ NSMutableString stringWithString: item ];
			
			isaNumber = TRUE;
		}
		
		// There may be fewer than 5 elements in nums[ ] after the row of data has been parsed
              if ( [ nums count ] >= 1 )
			dataItem1 = [ [ nums objectAtIndex: 0 ] retain ];
		if ( [ nums count ] >= 2 )
			dataItem2 = [ [ nums objectAtIndex: 1 ] retain ];
		if ( [ nums count ] >= 3 )
			dataItem3 = [ [ nums objectAtIndex: 2 ] retain ];
		if ( [ nums count ] >= 4 )
			dataItem4 = [ [ nums objectAtIndex: 3 ] retain ];
		if ( [ nums count ] >= 5 )
			dataItem5 = [ [ nums objectAtIndex: 4 ] retain ];
    }
	[ label retain ];
	
    return self;
}


----

Your code contains a division by zero (which is defined as 0 on ppc, but will throw an exception on x86, and is mathematically undefined).

**Actually what I was seeing in the table view was NaN**

You iterate     strings with an enumerator, and yet     utf8rep is taken as the first object in the     strings array.  *Oops. My bad. See below.*

From a design point-of-view your code is rather bad. You don't pass around arrays with vague hints about what they contain. If your window needs 4 integers and a label, you give it 4 integers and a label! The other just adds a lot of complexity (which is seeding ground for bugs and unmaintainable code). 
'My window needs to keep columns intact, not rows. Some rows will be short, or have holes. A slightly tougher problem.*

**Update:** I didn't read your code thoroughly, and see that your last stuff actually does initialize member data (so strike my first comment). That's what happens when you write >50 to receive a label and array of integers :)      *Yup, I *have* written some excess code, no doubt*

Did you consider just using an NSDictionary as your model class?
*I guess then I would use something like myDict.dataItem1 when binding the model key*

----

Thanks for pointing out the line that puts only the first string into utf8rep. That was left over from an earlier version
that assumed the label would *always* be in the first string. In the correction above, my intention is, I think, made clearer.

However, I am not receiving "only" a label and an array of floats. I am receiving variable-length records that I need
to parse as rationally as I can, but I guess I did not make that clear. The labels may not be in the first column,
but they will all be in a single column. It is a tougher problem than parsing a regular rectangular matrix.

If I were reading a rectangular matrix with no holes, of course I could do it with a string and an array of (int) (float), or whatever.

----

As for binding to a dictionary, yes, that's done by binding to     myDict.dataItem1 or similar.

I don't really understand the setup you have (maybe post a screen grab of the UI?) but granted that it is your own code which calls the initializer, clearly somewhere in your code you do know which column contain the label. So from my POV you have caller with concrete info, and callee which must reconstruct this info by parsing items in an array.

Btw: model code is supposed to be ignorant about the view. It does sound like you're pushing some UI choices down into the model, and making it handle it (which column contains a label is certainly not something the model should handle).

