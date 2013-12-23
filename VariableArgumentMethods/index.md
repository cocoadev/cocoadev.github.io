---
layout: page
title: VariableArgumentMethods
---

General/VariableArgumentMethods are methods that take a variable number of arguments, such as General/NSString stringWithFormat:.

stringWithFormat: accepts a formating argument, and then a number of arguments after that.

    
General/[NSString stringWithFormat: @"class is %@ and selector is %s", General/NSObject, @selector(init)];


this will return the General/NSString: @"class is General/NSObject and selector is init"

but how is this method written?.. Apple's documentation doesn't tell you much about writting methods such as stringWithFormat:.

As an example of how to do it.. we'll write a function that appends a variable number of strings, here it is:

    
+(General/NSString *)appendStrings:(General/NSString *)first, ... {
    General/NSMutableString * toReturn = General/[NSMutableString new];
    General/NSString * nextString = first;
    va_list ap;
    va_start(ap,first);
    while([nextString hasSuffix: @"&"]){
	[toReturn appendString: [nextString substringToIndex: [nextString length] - 1]];
	nextString = va_arg(ap,id);
    }
    [toReturn appendString: nextString];
    va_end(ap);
    return toReturn;
}


This method expects all it's arguments to be General/NSStrings.  Each argument should have a suffix of     @"&" if there is another argument.

so the proper way to call it is something like:
    
General/[MyClass appendStrings: @"one &", @"two &", @"three &", @"four"];

this will return @"one two three four"

note that:
    
General/[MyClass appendStrings: @"one &", @"two &", @"three &", @"four ", @"five "];

will return the same thing, because the method will not know about the fifth argument

also:
    
General/[MyClass appendStrings: @"one &", @"two &", @"three &", @"four &"];

will crash at runtime, as the method looks for another argument after     @"four &" and does not find one.


so... using variable argument methods means that you need to establish a convention about how they should be called.  Alternately, we could have written appendStrings: like this

    
+(General/NSString *)appendStrings:(int)count, ... {
    General/NSMutableString * toReturn = General/[NSMutableString new];
    General/NSString * nextString;
    va_list ap;
    va_start(ap,count);
    while(count > 0){
	nextString = va_arg(ap,id);
	[toReturn appendString: nextString];
	count--;
    }
    va_end(ap);
    return toReturn;
}


for that method, we would establish the convention of passing an int describing the number of strings to append, followed by all the strings.
like this:
    
General/[MyClass appendStrings: 4, @"one ", @"two ", @"three ", @"four "];


We could even split up the arguments like this:
    
+(General/NSString *)appendNumber:(int)count ofStrings:(General/NSString *)first, ... {
    General/NSMutableString * toReturn = General/[NSMutableString new];
    General/NSString * nextString = first;
    va_list ap;
    va_start(ap,first);
    while(count > 0){
	[toReturn appendString: nextString];
	nextString = va_arg(ap,id);
	count--;
    }
    [toReturn appendString: nextString];
    va_end(ap);
    return toReturn;
}


and the convention would be like this:
    
General/[MyClass appendNumber: 4 ofStrings: @"one ", @"two ", @"three ", @"four"];




**What are people's thoughts on the conventions for passing a variable number of arguments to a method?**

Which of the two shown do you think is a better convention?  And... what other interesting conventions can you think of for this situation?  It is important to come up with a good memorable convention, because if you call it wrong you'll probably crash.

----

Both conventions stink.  Use the existing C/Cocoa Conventions: either have a format argument that defines what's coming later (think printf or General/NSString's stringWithFormat:), or use a nil sentinel to indicate the end of a list of strings (like General/NSArray's arrayWithObjects:".  No need to count items (hard to maintain, and a pain if you have more than a handful of items) or have some hack that you have to attach to your strings (if you decide to rearrange them).

----

I prefer the nil termination.  If that looks ugly you can have something like,

#define THE_ARGS(x) x, nil

then it might be a little more pleasing to the eye to see General/[NSArray arrayWithObjects: THE_ARGS(obj1,obj2,obj3)];

-General/FranciscoTolmasky
----
    
@interface General/NSMutableString (General/MYAdditions)

- (void)appendStrings:(General/NSString *)aString, ...;

@end

@implementation General/NSMutableString (General/MYAdditions)

- (void)appendStrings:(General/NSString *)aString, ...
{
	va_list ap;
	va_start(ap, aString);
	
	General/NSString *str;
	
	[self appendString:aString];
	
	while (str = va_arg(ap, id))
		[self appendString:str];
	
	va_end(ap);
}

@end
.
.
.
	General/NSMutableString *str = General/[NSMutableString string];
	if (nil != str) {
		[str appendStrings:@"How ", @"Now ", @"Brown ", @"Cow.", nil];
		General/NSLog(str);
	}


However, the above does not support [str appendStrings:nil]. I recommend the following instead, which is simpler and supports [str appendStrings:nil]:

    
- (void)appendStrings:(General/NSString *)aString, ...
{
	va_list ap;
	va_start(ap, aString);
	
	General/NSString *str;
	
	for (str = aString; str; str = va_arg(ap, id))
		[self appendString:str];
	
	va_end(ap);
}


----

This is great and very useful, but what if you want to pass the argument list to another function?  For example it would be really convenient to write a function like:

    

- (void) doAlert:(General/NSString *) msg withInfoFormat:(General/NSString *) infoFormat,...
{
  //get the info string by calling stringWithFormat with argument list...
  General/NSString *info = General/[NSString stringWithFormat:<THE ARGUMENT LIST>];

  ...
}



How would one refer to the argument list without iterating over it?  Or is that just not possible?


----

It's possible, but you have to use the General/NSString method specifically designed for it:

    
- (id)initWithFormat:(General/NSString *)format arguments:(va_list)argList


----

here is an example that worked for me:

    
- (void) postFormattedErrorMessage:(General/NSString *) format,...
{
  va_list args;
  va_start(args,format);

  General/NSString *msg = General/[[NSString alloc] initWithFormat:format arguments:args];
  [self postErrorMessage:msg];
  
  va_end(args);
  [msg release];
}



----

gcc also supports variadic arguments in macros. For example, you can do this:

    
#define myMacro(a, b, ...) [ self myMethodWithA:a andVariableArgs:b, __VA_ARGS__ ]


--General/TimHart


To build on what Tim said, it also supports:

    
#define myMacro(a, b, ...) [ self myMethodWithA:a andVariableArgs:b, ## __VA_ARGS__ ]


If no variables are specified after b, then ## removes the trailing ',' from b and __VA_ARGS__ doesn't generate anything.

--General/BobWhite
