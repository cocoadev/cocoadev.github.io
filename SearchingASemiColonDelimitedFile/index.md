---
layout: page
title: SearchingASemiColonDelimitedFile
---

I have a semicolon delimited file that contains ICAO codes and related info. I want to be able to search this fast enough so results appear as a user is typing a search string ala iTunes.

I have set it up already so that I use     componentsSeparatedByString:@"\r" to get each line, and then for each line, I split up by semicolon and put the relevant data into an NSDictionary. The file is 755KB and contains 11,574 lines. Right now it takes about 1-1.5 (not official) seconds to finish searching, and I want to get multiple results so I have to go to the very end. I only want to search the country, city, state and code fields, and nothing else so I can't just check to see if each line contains the     searchString.

Here's how I do it. Could you give me tips on making it faster and more efficient? What about loading each line into it's own object at     init, and then searching an array of my own objects, or would that be worse?

    
- (id)init
{
	if (self = [super init])
	{
		NSString *path = // path to file;
		codes = [[NSString alloc] initWithContentsOfFile:path];
	}
	return self;
}

- (NSArray *)searchFor:(NSString *)searchString
{
	NSArray *lines = [codes componentsSeparatedByString:@"\r"];
	NSEnumerator *e = [lines objectEnumerator];
	NSString *line;
	NSMutableArray *results = [NSMutableArray array];
	while (line = [e nextObject])
	{
		NSDictionary *d = [self infoForLine:line];
		if ([d count]>0 && (d objectForKey:@"country"] containsString:searchString] ||
							[[d objectForKey:@"state"] containsString:searchString] ||
							[[d objectForKey:@"city"] containsString:searchString] ||
							[[d objectForKey:@"code"] containsString:searchString]))
			[results addObject:d];
	}
	
	return [[[NSArray arrayWithArray:results];
}

- (NSDictionary *)infoForLine:(NSString *)line
{
	if ([line length]==0)
		return [NSDictionary dictionary];
	
	NSArray *items = [line componentsSeparatedByString:@";"];
	if ([items count]<=1 || [items count]<6)
		return [NSDictionary dictionary];
	
	NSString *country, *state, *city, *code;
	code = [items objectAtIndex:2];
	city = [items objectAtIndex:3];
	state = [items objectAtIndex:4];
	country = [items objectAtIndex:5];
	return [NSDictionary dictionaryWithObjectsAndKeys:
		country, @"country",
		state, @"state",
		city, @"city",
		code, @"code",
		nil];
}

// NSString catagory (in a separate file)
@interface NSString (xtras)

- (BOOL)containsString:(NSString *)aString;

@end

@implementation NSString (xtras)

- (BOOL)containsString:(NSString *)aString
{
    unsigned mask = NSCaseInsensitiveSearch;
    NSRange range = [self rangeOfString:aString options:mask];
    return (range.length > 0);
}

@end


Here's a few sample lines from the file:

    
72;466;KCOS;Colorado Springs, City Of Colorado Springs Municipal Airport;CO;United States;4;38-48-57N;104-42-39W;38-48-31N;104-43-14W;1881;1856;
72;468;KFCS;Fort Carson;CO;United States;4;38-42N;104-46W;38-42N;104-46W;1789;1789;
72;469;KDNR;Denver / Stapleton International, Co.;CO;United States;4;39-47N;104-52W;39-45N;104-52W


----

If you insist on using arrays of dictinoaries, you should *definately* be creating them either on -init or when they're first used, because not only is creating all those objects expensive, -componentsSeparatedByString: will be a serious bottleneck in your code.

----

Try switching to NSScanner to get each line sequentially instead of using     componentsSeparatedByString:.  That'll knock it down by a good bit since you'll go through the file one fewer times.  Also, is this data that's changing a lot?  If not, you should cache your array of dictionaries rather than recomputing it for every search.  Even if it is changing, see if you can update your existing data rather than creating it all from scratch.  There are always tradeoffs btw. speed and simplicity, but if you diff the new file against the old one you can find just the lines that have changed, and update just those elements of your data.  You may want to switch to a dictionary instead of an array to store your data, and then is one of those numbers a primary key?

----
Also...it might just be easier to use a PropertyList...which is also usually easier to understand, and is already supported. --JediKnil

*There's probably a reason he's asking the question like this.  For example, he didn't produce the data file, or that it's periodically downloaded.*

----

I ended up on     init creating the array of lines, and then instead of using NSDictionary's, I created a simple class to store the info. So when I search, I just go through the array, and no new objects have to be initialized in the loop. Works very fast :)

