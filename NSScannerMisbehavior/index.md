---
layout: page
title: NSScannerMisbehavior
---



I cannot get NSScanner to properly scan data into my application.  The data is in the form of an ASCII file with each datum on a separate line.  Additionally, the data can be empty, and thus just an empty line.  So the data file looks something like this...
    19.250
25.062
21.250
14.875
21.875

40.062
75.188
27.120
26.170

23.320
11.000
16.250
8.000
6.375
6.375
14.250
25.375

The code that I am using to load this is
    float value;
NSString *key;
id myObject;
NSArray *arrayOfKeys = [NSArray arrayWithObjects:@"foo1", @"foo2", @"foo3", nil]
NSEnumerator *e = [arrayOfKeys objectEnumerator];
        
NSString *aString = [NSString stringWithCString:[data bytes] length:[data length]];
// The data is the file as passed into -loadDataRepresentation: ofType

NSScanner *aScanner = [NSScanner scannerWithString:aString];
[aScanner setCharactersToBeSkipped:[NSCharacterSet whitespaceCharacterSet]];

myObject = Object alloc] init];

while ( key = [e nextObject])
{
   if ([aScanner scanFloat:&value])
   {
       [myObject takeValue:[[[NSNumber numberWithFloat:value] forKey:key];
   }
   else
   {
       [myObject setUseData:FALSE forKey:key];
   }
}

I have to tell my object when a nil value occurs in the data file.  And because nil values can occur at the beginning of the file, I had to tell aScanner to not ignore new line characters.  Why is this code not working?
----
You never said what the code does. It's obvious what it's supposed to do, but you just said it's "not working". How does it not work, what does it do?

On another subject, never use     stringWithCString:. You should always use     stringWithUTF8String: or one of the     initWithBytes:... or     initWithData:... methods.
----
My bad.  It loads the first data point, and then doesn't load any others.  Looking at [aScanner scanLocation] it looks like on the second iteration the location goes to a very large number, what I can only assume is the end of the data file, without finding any other float values.
----
Here is a variation that does what you wish. I changed the code slightly to make it command-line tool for demonstration, but that doesn't affect the logic:
    
#import <Foundation/Foundation.h>

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

	NSCharacterSet *newlineCharacterSet = [NSCharacterSet characterSetWithCharactersInString:@"\n"];
	float value;
	NSString *key;
	id myObject;
	NSArray *arrayOfKeys = [NSArray arrayWithObjects:@"foo1", @"foo2", @"foo3", nil];
	NSEnumerator *e = [arrayOfKeys objectEnumerator];
	
	NSData *data = [NSData dataWithContentsOfFile:@"/Users/AUser/Projects/ScannerMalfunction/TheData.txt"];
	NSString *aString = [NSString stringWithCString:[data bytes] length:[data length]];
	
	NSScanner *aScanner = [NSScanner scannerWithString:aString];
	[aScanner setCharactersToBeSkipped:[NSCharacterSet whitespaceCharacterSet]];
	
	myObject = [[NSMutableDictionary alloc] init];
	
	while ( key = [e nextObject])
	{
		if ([aScanner scanFloat:&value])
		{
			[myObject takeValue:[NSNumber numberWithFloat:value] forKey:key];
		}
		else
		{
			[myObject takeValue:[NSNull null] forKey:key];
		}

		[aScanner scanUpToCharactersFromSet:newlineCharacterSet intoString:nil];  // Find the newline
		[aScanner setScanLocation:([aScanner scanLocation] + 1) ];   // Skip the newline
	}
    
	NSLog(@"%@", myObject);
	
	[pool release];
    return 0;
}

Your original code fails because the scanner can't get past the newline following the first float. You set the scanner to skip whitespace only, so you must account for the newline.

aburgh

