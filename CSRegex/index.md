---
layout: page
title: CSRegex
---

CSRegex is a simple Objective-C wrapper around the     regcomp()/    regexec() functions available in the BSD libc. It is meant to be a quick and simple regex class that can be dropped into any Cocoa project with a minimum of fuss. It consists of only two files, and no external dependencies. Currently there is no documentation, but I hope to remedy that soon. It should, however, be pretty self-documenting.

It is simpler to use than other regex wrappers, such a OgreKit, AGRegex or OmniFoundation, but not as powerful. It is meant for code that needs simple regex snippets, without the need for including external libraries or frameworks. 

Note that the use of the BSD regex functions means this class uses POSIX regexes and not Perl regexes! This most notably means that character classes like     \w and     \s do not work - POSIX uses character classes like     [:alpha:] and     [:space:] instead. Use the command "    man re_format" for more information.

Two missing features that would be nice to have implemented are search-and-replace regexs, and a function to find all substrings that match a given pattern. I might implement these at some point, unless somebody else beats me to it.

It is released into the public domain. I claim no copyright on any part of it. -- WAHa

----

Updated on 2006.06.26 to add the     escapedPattern method to NSString to create a properly escaped version of a string for use when building regexes programmatically. -- WAHa

----

I have since updated this code, but until I find the time to document and write some test cases, I won't post it. If you still want it, it's available at http://code.google.com/p/xee/source/browse/trunk/CSRegex.m and http://code.google.com/p/xee/source/browse/trunk/CSRegex.h. It supports matching a pattern multiple times against the same string to find all matches. - WAHa

----

Here is     CSRegex.h:

    


#import <Foundation/Foundation.h>
#import <regex.h>

@interface CSRegex:NSObject
{
	regex_t preg;
}

-(id)initWithPattern:(NSString *)pattern options:(int)options;
-(void)dealloc;

-(BOOL)matchesString:(NSString *)string;
-(NSString *)matchedSubstringOfString:(NSString *)string;
-(NSArray *)capturedSubstringsOfString:(NSString *)string;

+(CSRegex *)regexWithPattern:(NSString *)pattern options:(int)options;
+(CSRegex *)regexWithPattern:(NSString *)pattern;

+(NSString *)null;

+(void)initialize;

@end

@interface NSString (CSRegex)

-(BOOL)matchedByPattern:(NSString *)pattern options:(int)options;
-(BOOL)matchedByPattern:(NSString *)pattern;

-(NSString *)substringMatchedByPattern:(NSString *)pattern options:(int)options;
-(NSString *)substringMatchedByPattern:(NSString *)pattern;

-(NSArray *)substringsCapturedByPattern:(NSString *)pattern options:(int)options;
-(NSArray *)substringsCapturedByPattern:(NSString *)pattern;

-(NSString *)escapedPattern;

@end



----

Here is     CSRegex.m:

    

#import "CSRegex.h"

static NSString *nullstring=nil;

@implementation CSRegex

-(id)initWithPattern:(NSString *)pattern options:(int)options
{
	if(self=[super init])
	{
		int err=regcomp(&preg,[pattern UTF8String],options|REG_EXTENDED);
		if(err)
		{
			char errbuf[256];
			regerror(err,&preg,errbuf,sizeof(errbuf));
			[NSException raise:@"CSRegexException"
			format:@"Could not compile regex \"%@\": %s",pattern,errbuf];
		}
	}
	return self;
}

-(void)dealloc
{
	regfree(&preg);
	[super dealloc];
}

-(BOOL)matchesString:(NSString *)string
{
	if(regexec(&preg,[string UTF8String],0,NULL,0)==0) return YES;
	return NO;
}

-(NSString *)matchedSubstringOfString:(NSString *)string
{
	const char *cstr=[string UTF8String];
	regmatch_t match;
	if(regexec(&preg,cstr,1,&match,0)==0)
	{
		return [[[NSString alloc] initWithBytes:cstr+match.rm_so
		length:match.rm_eo-match.rm_so encoding:NSUTF8StringEncoding] autorelease];
	}

	return nil;
}

-(NSArray *)capturedSubstringsOfString:(NSString *)string
{
	const char *cstr=[string UTF8String];
	int num=preg.re_nsub+1;
	regmatch_t *matches=calloc(sizeof(regmatch_t),num);

	if(regexec(&preg,cstr,num,matches,0)==0)
	{
		NSMutableArray *array=[NSMutableArray arrayWithCapacity:num];

		int i;
		for(i=0;i<num;i++)
		{
			NSString *str;

			if(matches[i].rm_so==-1&&matches[i].rm_eo==-1) str=nullstring;
			else str=[[[NSString alloc] initWithBytes:cstr+matches[i].rm_so
			length:matches[i].rm_eo-matches[i].rm_so encoding:NSUTF8StringEncoding] autorelease];

			[array addObject:str];
		}

		free(matches);

		return [NSArray arrayWithArray:array];
	}

	free(matches);

	return nil;
}

+(CSRegex *)regexWithPattern:(NSString *)pattern options:(int)options
{ return [[[CSRegex alloc] initWithPattern:pattern options:options] autorelease]; }

+(CSRegex *)regexWithPattern:(NSString *)pattern
{ return [[[CSRegex alloc] initWithPattern:pattern options:0] autorelease]; }

+(NSString *)null { return nullstring; }

+(void)initialize
{
	if(!nullstring) nullstring=[[NSString alloc] initWithString:@""];
}

@end

@implementation NSString (CSRegex)

-(BOOL)matchedByPattern:(NSString *)pattern options:(int)options
{
	CSRegex *re=[CSRegex regexWithPattern:pattern options:options|REG_NOSUB];
	return [re matchesString:self];
}

-(BOOL)matchedByPattern:(NSString *)pattern
{ return [self matchedByPattern:pattern options:0]; }

-(NSString *)substringMatchedByPattern:(NSString *)pattern options:(int)options
{
	CSRegex *re=[CSRegex regexWithPattern:pattern options:options];
	return [re matchedSubstringOfString:self];
}

-(NSString *)substringMatchedByPattern:(NSString *)pattern
{ return [self substringMatchedByPattern:pattern options:0]; }

-(NSArray *)substringsCapturedByPattern:(NSString *)pattern options:(int)options
{
	CSRegex *re=[CSRegex regexWithPattern:pattern options:options];
	return [re capturedSubstringsOfString:self];
}

-(NSArray *)substringsCapturedByPattern:(NSString *)pattern
{ return [self substringsCapturedByPattern:pattern options:0]; }

-(NSString *)escapedPattern
{
	int len=[self length];
	NSMutableString *escaped=[NSMutableString stringWithCapacity:len];

	for(int i=0;i<len;i++)
	{
		unichar c=[self characterAtIndex:i];
		if(c=='^'||c=='.'||c=='['||c=='$'||c=='('||c==')'
		||c=='|'||c==**'||c=='+'||c=='?'||c=='{'||c=='\\') [escaped appendFormat:@"\\\\%C",c];
		else [escaped appendFormat:@"%C",c];
	}
	return [NSString stringWithString:escaped];
}

@end



----

Here is a simple test program:

    

#import "CSRegex.h"

int main(int argc,char **argv)
{
	NSAutoreleasePool *pool=[[NSAutoreleasePool alloc] init];

	NSString *str=@"Jackdaws love my big sphinx of quartz";

	NSLog(@"\n%d\n%d\n%@\n%@",
		[str matchedByPattern:@"love.*hinx"],
		[str matchedByPattern:@"quick brown fox"],
		[str substringMatchedByPattern:@"love.*hinx"],
		[str substringsCapturedByPattern:@"(love) (.*) (.*hinx)"]
	);

	[pool release];
}



The output of this program should be something along the lines of:

    

2006-05-10 16:35:05.579 test[2792]
1
0
love my big sphinx
("love my big sphinx", love, "my big", sphinx)



----[@"red blue "  matchedByPattern:@"\w+"] or [@"red blue "  matchedByPattern:@"\s"] returns NO. Is it normal  or did I miss something ?

----
You need to double-up your backslashes.     @"\w+" just produces a string that contains     w+, because gcc is parsing the string and parsing backslash escapes before the regex engine can see them. It should have warned you about     \w being an unknown backslash escape. Use     @"\\w+" instead.

----
Thanks. Yes � but No. I should have double-up my backshlashes, for sure, but it still doesn't work. It seems that these shortcuts are not reconized by the built-in regex engine. Anyway,     \s and     \w are shortcuts for character classes so     \w stands for [a-zA-Z] and     \s stands for a whitespace character. It works that way for me.

----
CSRegex uses, as mentioned, the BSD libc regex function, and those use POSIX regexs.     \w and     \s are Perl regex shortcuts that do not exist in POSIX regexs. POSIX uses character classes like     [:alpha:] and     [:space:] instead. Do a     man re_format for more information on the format used. -- WAHa

----
I used CSRegex in a recent project and it was quick and easy.  I looked into OgreKit, but it seemed too big for what I needed to do. --BillGarrison

----
I try to use this code and I get in error on the for loop.  Says I can't use for outside of C99 mode?  Never heard of that before?  --TimDavis

----
In C89 mode you cant declare a variable in  a for loop. You can either replace the line "for(int i=0;i<len;i++)" in -escapedString with
    int i;
for(i=0;i<len;i++) or add -std=gnu99 to compiler options. --ACoolie

----
I think the code has a memory leak (?) Paulcd

----
I see two possible memory leaks. In -capturedSubstringsOfString, matches is not freed unless there is a match. The memory is reserved but never used. And nullstring ivar is not freed. It is explicitly alloced, but it is a string constant @"". I don't know if allocing a string constant increments its retain count. I am guessing it doesn't I remember reading @"" is a singleton and the same instance every time. Do you see any others? --ACoolie

----
Yes, the first one is true, I just noticed it independetly when tracking down memory leaks in one of my apps. I've fixed it.

The second isn't - nullstring isn't an ivar, it's a static global, and used as a singleton. -- WAHa

----
But since the memory for the string is alloced before the string is initialized with the constant, won't the memory still be reserved?

----
Of course, but it is only ever done once, and then the same object is used throughout the lifetime of the program. It is never meant to be released. -- WAHa

----
Let's say I want to match the bold markup expression <b>.*</b> -- Given the string "There are <b>two</b> bold words in <b>this</b> sentence" CSRegex is matching everything between the first opening bold marker and the last closing marker. It only sees one long match instead of two separate ones. Is there a special options flag I need to pass to the wrapper so that it sees two distinct matches instead of one, or is this the nature of regular expression matching?

----
I don't know the exact answer to your question but the keyword you're looking for is "greedy". Regexes are usually greedy, which means that they will search for the longest possible match to your expression. Some regex engines allow you to turn this off, which will cause them to find the shortest match instead. I don't know about this one but that's probably the term you'll need to look for.

----
* is greedy.  You want what's known as *? (non-greedy Kleene star) in PCRE.  I'm not sure how POSIX regex does that.

----
The Mac OS Regex Manual ( http://developer.apple.com/documentation/Darwin/Reference/ManPages/man3/regcomp.3.html ) says that in the Mac OS X POSIX implementation "A repetition operator (`?', `**, `+', or bounds) cannot follow another repetition operator.  A repetition operator cannot begin an expression or subexpression or follow `^' or `|'." Is it possible that POSIX doesn't support what I'm trying to do and that I should just switch to PCRE?

----
You can try using     <b>[^<>]*</b>. That will not match something like "<b>Blah blah<br />blah blah</b>", though, which depending on what you are doing that may or may not be a problem. Of course, using regexps to extract data from HTML is always a kludge. If you want a tool better suited for the job, check out NSXMLDocument and XPath. -- WAHa

----

Here is a first pass at a method to return all substrings that match a given pattern:

    
- (NSArray *) matchedSubstringsOfString: (NSString *) string
{
  NSMutableArray * result = [NSMutableArray array];
  const char *cstr=[string UTF8String];
  const char * strptr = cstr;
  regmatch_t match;
  int error = regexec(&preg,strptr,1,&match,0);
  while (error == 0) {
    NSString * matchingSubstring = [[[NSString alloc] initWithBytes: strptr+match.rm_so
                                                             length: match.rm_eo-match.rm_so
                                                           encoding: NSUTF8StringEncoding]
                                    autorelease];
    [result addObject: matchingSubstring];
    strptr += match.rm_eo;
    error = regexec(&preg,strptr,1,&match,REG_NOTBOL);
  }    
  return result;
}


-- geechorama

----
Actually, I already implemented a set of functions for applying a regex multiple times to a string in a somewhat more generalized manner, but I've been too lazy to write up any examples so I hadn't posted it. If anyone wants it, speak up. -- WAHa

----
The "null singleton" idiom used above is completely broken by     NSString's optimizations. While it's not guaranteed, in the current implementation,     [[NSString alloc] initWithString:@""] == @"". Because of how strings are reused, testing for pointer equality is no different than using     -isEqualToString:. That means that all empty strings are considered null strings in the code above. It's better to use     NSNull (like it was intended) or a null string subclass. I have pointed this out to WAHa personally, but it's worth noting here as well.
----
Does this support unicode? I have a similar regex.h wrapper class, but it all falls apart when I use it on NSString objects containing Japanese -- TomD

