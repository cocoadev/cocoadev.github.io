---
layout: page
title: ReadFileLines
---



lets say i have a file like this
    
set UserName <User>
set Server <localhost>
set Port <5154>
set Email <myself@myserver.com>

How do i read those lines into an array,
and from the array split the words so i can get the string within <>?

----

You can use the NSString functions stringWithContentsOfFile and componentsSeperatedByString to generate an NSArray containing each string on it's own line, like so:

    
    NSString *tmp;
    NSArray *lines;
    lines = [[NSString stringWithContentsOfFile:@"testFileReadLines.txt"] 
                       componentsSeparatedByString:@"\n"];
    
    NSEnumerator *nse = [lines objectEnumerator];
    
    while(tmp = [nse nextObject]) {
        NSLog(@"%@", tmp);
    }


DaveGiffin http://www.davtri.com

----

Then use NSScanner to get the string between the <>s:

    
    NSString *tmp;
    NSArray *lines;
    lines = [[[NSString stringWithContentsOfFile:filePath] 
                        stringByStandardizingPath] 
                        componentsSeparatedByString:@"\n"];
    
    NSEnumerator *nse = [lines objectEnumerator];
    
    while(tmp = [nse nextObject]) {
        NSString *stringBetweenBrackets = nil;
        NSScanner *scanner = [NSScanner scannerWithString:tmp];
        [scanner scanUpToString:@"<" intoString:nil];
        [scanner scanString:@"<" intoString:nil];
        [scanner scanUpToString:@">" intoString:&stringBetweenBrackets];

        NSLog(@"%@", stringBetweenBrackets);
    }


----
 The code just ouput the last configuration in the file, in this case the email.
How do i turn all the String Between the Brackets into an array?

*Add them to a NSMutableArray in the while loop*

i also have problems reading the file
    
//NSArray *lines = [[NSString stringWithContentsOfFile:filePath] 
//                            componentsSeparatedByString:@"\n"];

NSArray *lines = [NSArray arrayWithObjects: @"set UserName <Username>", 
                                            @"set Server <localhost>", 
                                            @"set Port <5154>", 
                                            @"set Email <myemail@myserver.com>", 
                                            nil];

if i use the first array (from file) nothing outputs, but if use the second array ( i assume the array from the file will be like that) it work perfectly well.
What's wrong with the readfile code is there a initWithFile that will work better?

*You need to expand any tildes in the path. See the code above.*

Thanks

----

Is this your format? Wouldn't it be easier to use a PropertyList?

----
The file still dont work
    
- (void)awakeFromNib
{
	NSString *stringBetweenBrackets = nil;
	NSString *tmp;
	
	NSArray *lines = [[NSString stringWithContentsOfFile:@"~/Library/Application Support/myApp/config.txt"]
									componentsSeparatedByString:@"\n"];

	NSEnumerator *nse = [lines objectEnumerator];
	NSMutableArray *values = [NSMutableArray new];
	
	while(tmp = [nse nextObject])
	{
		NSScanner *scanner = [NSScanner scannerWithString:tmp];
        [scanner scanUpToString:@"<" intoString:nil];
        [scanner scanString:@"<" intoString:nil];
        [scanner scanUpToString:@">" intoString:&stringBetweenBrackets];
		[values addObject:stringBetweenBrackets];
    }
	
	[userField setStringValue:values objectAtIndex:0] retain;
	[serverField setStringValue:values objectAtIndex:1] retain;
	[portField setStringValue:values objectAtIndex:2] retain;
	[emailField setStringValue:values objectAtIndex:3] retain;
}

I use this code for this file
    
set UserName <User>
set Server <localhost>
set Port <5154>
set Email <myself@myserver.com>

and when i open the application it unexpectedly quits every time
whats is wrong?

----

1. Read RetainingAndReleasing.
2. This is apparently a config file for your app. Use a plist and you won't have to do any of this.

----

I put this into a foundation tool, changing the userField, etc. into NSString objects. Here is the code
    
#import <Foundation/Foundation.h>

NSString *userField;
NSString *serverField;
NSString *portField;
NSString *emailField;

void doit() {
    NSString *stringBetweenBrackets = nil;
    NSString *tmp;
    
    NSArray *lines = [[NSString stringWithContentsOfFile:@"testFileReadLines.txt"] componentsSeparatedByString:@"\n"];

    NSEnumerator *nse = [lines objectEnumerator];
    NSMutableArray *values = [NSMutableArray new];
    
    while(tmp = [nse nextObject])
    {
        NSScanner *scanner = [NSScanner scannerWithString:tmp];
        [scanner scanUpToString:@"<" intoString:nil];
        [scanner scanString:@"<" intoString:nil];
        [scanner scanUpToString:@">" intoString:&stringBetweenBrackets];
        [values addObject:stringBetweenBrackets];
    }
    
    userField = values objectAtIndex:0] retain];
    serverField = [[values objectAtIndex:1] retain];
    portField = [[values objectAtIndex:2] retain];
    emailField = [[values objectAtIndex:3] retain];
    
    [[NSLog(@"\nuser:%@\nserver:%@\nport:%@\nemail:%@\n", userField, serverField, portField, emailField);
}

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    NSString *tmp;
    NSArray *lines;
    lines = [[NSString stringWithContentsOfFile:@"/Users/remster/testFileReadLines.txt"] componentsSeparatedByString:@"\n"];
    
    NSEnumerator *nse = [lines objectEnumerator];
    
    while(tmp = [nse nextObject]) {
        NSLog(@"%@", tmp);
    }

    NSLog(@"\n\n");
    doit();
    
    [pool release];
    return 0;
}



I hope this helps.

By the way, I agree with the above... Use a plist and your life would be so much easier. Or even better, just use [NSUserDefaults standardUserDefaults].

DaveGiffin http://www.davtri.com

----

I also just noticed you're using a tilde (~) in your path, but not standardizing your path, which might be why on your tests, when it wasn't crashing, it wasn't outputting any data. You need to do something like this:

    
NSString *path;
path = [@"~/Library/Application Support/myApp/config.txt" stringByStandardizingPath];


this way your @"~/Library/Application Support/myApp/config.txt" will be transformed into @"/Users/currentUserName/Library/Application Support/myApp/config.txt"


DaveGiffin http://www.davtri.com

----
Ah thanks alot now it work, it was the tilde.

----

No problem, glad I could help :-)

----

Of course, the code is pointless, since reading/writing a PropertyList is a one-liner.

*I shall look into PropertyList, i just needed this first so i can choose*

----

Yep, like I said above, a property list is much, much easier to deal with, not to mention you don't have to worry about typos in your config files (unless you're manually editing them).

DaveGiffin

----

Instead of just writing a PropertyList at some random location in the filesystem, why not use NSUserDefaults? This is Cocoa. Doing all the usual busywork just to get standard behavior out of your app is rarely necessary.

----

While working with a plist is so much easier than reading from a data file, there are times when a plist just isn't available. For example, in most scientific and engineering work, you will have a large, possibly unencoded text file for your data. A good example of this is the space business where you have the same chance of finding the Holy Grail as finding a file of keplerian elements for satellites formatted as a plist. 

Apple's documentation, and suggestions I've found on the web, have been of limited help in writing a scanner to read a varied data file. For my part, Apple documentation for NSScanner is oblique. As is somewhat common throughout, the documentation for NSScanner is overly simplisting and frustratingly brief. I would, and I believe I'm not the only one here to feel this way, like Apple to offer up more example code in its documentation and even go so far as to have 1-2 example programs available to show working solutions for Class references such as NSScanner, NSNumber, NSCalendarDate, NSDecimalNumber, and so on. The SciTech programming community's needs have not been forcefully met by Apple, forcing those of us in this industry to either figure it out ourselves, which is I guess what we are paid for, or to revert to C, which isn't why I learned Cocoa. There are people such as Malcolm Crawford who patrol through Apple's Cocoa Dev mailing list to put out fires. All of us can be thankful for Apple-people like Malcolm.

Speaking of whom, a much more elegant solution than my original posting was proposed by mmalc. In light of lessons learned form Malcolm, I nuked my original code and adopted his suggested code. Thanks Malcolm and sorry if the things I write hit a sore spot. Like most here, I'm just trying to make the experience for the uninitiated (untortured?) less...well, tortuous.

So, compliments of OrbitCode and yours truly Jim Hillhouse, as well as Malcolm Crawford and Adam Maxwell, here you go:

    
<code>
/*
Small sample of Keplerian data containing 

Body    Mean Radius mu          eccentricity
============================================
Moon    1738.0      4902.799    0.05490
Earth   6378.1363   398600.4    0.016708617
*/

- (void)readDataFileAndStore 
{
    
    NSString *bodyName;
    NSNumber *meanRadius;
    NSNumber *gravParameter;
    NSNumber *eccentricity;
    
    
    float   mR;
    float   gP;
    float   ecc;
    
    NSString *dataFilePath;
    dataFilePath = [[NSBundle mainBundle] pathForResource:@"PlanetaryData" 
                                                   ofType:@"txt"];  
    NSError *error;
    
    NSString *dataSource = [NSString stringWithContentsOfFile:dataFilePath encoding:NSUTF8StringEncoding error:&error];
    
    if (dataSource == nil) 
    {
        NSLog(@"There a problem with the data...it's not there!");
    }
    
    NSScanner *scanner = [[NSScanner alloc] initWithString:dataSource];
    
    NSCharacterSet *letterCharacterSet = [NSCharacterSet letterCharacterSet];
    
    while ([scanner isAtEnd] == NO)
    {
        if (
            [scanner scanCharactersFromSet:letterCharacterSet intoString:&bodyName] &&
            [scanner scanFloat:&mR] &&
            [scanner scanFloat:&gP] &&
            [scanner scanFloat:&ecc]
            )
        NSLog(@"Output:");
        NSLog(@"%@: %1.2f, %1.2f, %1.2f", bodyName, mR, gP, ecc);
        
        [planetaryArray addObject:bodyName];
        [planetaryArray addObject:[NSNumber numberWithFloat:mR]];
        [planetaryArray addObject:[NSNumber numberWithFloat:gP]];
        [planetaryArray addObject:[NSNumber numberWithFloat:ecc]];
        
        // Put these buggars into Planet
        
    }

    bodyName        = planetaryArray objectAtIndex:0] retain];
    meanRadius      = [[planetaryArray objectAtIndex:1] retain];
    gravParameter   = [[planetaryArray objectAtIndex:2] retain];
    eccentricity    = [[planetaryArray objectAtIndex:3] retain];
    
    [[NSLog(@"\nbody:%@\nradius:%@\ngravParam:%@\neccen:%@\n", bodyName, meanRadius, gravParameter, eccentricity);
}
</code>


Here's the caveat--if reading a large file, Adam Maxwell has found that it's possible that the autorelease pool could be overwhelmed. As recommended by Adam, check with Shark to see if that is happening, something all of us should be doing. If the autorelease pool is being overwhelmed, one work around is:

    
<code>
- (void)readDataFileAndStore 
{
    
    NSString *bodyName;
    NSNumber *meanRadius;
    NSNumber *gravParameter;
    NSNumber *eccentricity;
    
    
    float   mR;
    float   gP;
    float   ecc;
    
    NSString *dataFilePath;
    dataFilePath = [[NSBundle mainBundle] pathForResource:@"PlanetaryData" 
                                                   ofType:@"txt"];  
    NSError *error;
    
    NSString *dataSource = [NSString stringWithContentsOfFile:dataFilePath encoding:NSUTF8StringEncoding error:&error];
    
    // Need to be careful here as not all files end with \n, but could also end with \r, etc.
    NSArray *lines = [[NSString stringWithContentsOfFile:dataSource] componentsSeperatedByString:@"\n"];

    NSNumberator *nse = [lines objectEnumerator];
    
    if (dataSource == nil) 
    {
        NSLog(@"There a problem with the data...it's not there!");
    }
    
    NSScanner *scanner = [[NSScanner alloc] initWithString:dataSource];
    
    NSCharacterSet *letterCharacterSet = [NSCharacterSet letterCharacterSet];
    
    while (temp = [nse nextObject])
    {
        NSScanner *theScanner = [[NSScanner alloc] initWithString:source];  // I'll release this below.
        
        if (
            [scanner scanCharactersFromSet:letterCharacterSet intoString:&bodyName] &&
            [scanner scanFloat:&mR] &&
            [scanner scanFloat:&gP] &&
            [scanner scanFloat:&ecc]
            )
        NSLog(@"Output:");
        NSLog(@"%@: %1.2f, %1.2f, %1.2f", bodyName, mR, gP, ecc);
        
        [planetaryArray addObject:bodyName];
        [planetaryArray addObject:[NSNumber numberWithFloat:mR]];
        [planetaryArray addObject:[NSNumber numberWithFloat:gP]];
        [planetaryArray addObject:[NSNumber numberWithFloat:ecc]];
        
        // Put these buggars into Planet
        
    }
    
    [scanner release]; // To keep from overwhelming the autorelease pool.
            

    bodyName        = planetaryArray objectAtIndex:0] retain];
    meanRadius      = [[planetaryArray objectAtIndex:1] retain];
    gravParameter   = [[planetaryArray objectAtIndex:2] retain];
    eccentricity    = [[planetaryArray objectAtIndex:3] retain];
    
    [[NSLog(@"\nbody:%@\nradius:%@\ngravParam:%@\neccen:%@\n", bodyName, meanRadius, gravParameter, eccentricity);
}
</code>



----

Yup, sometimes you just have to deal with these types of files. I had been working on an app that, sometimes (phase of the moon), the input file would have a mix of \n and \r. From line to line it could change and why I don't know. A simple guard against that is to replace one with the other via     replaceOccurrencesOfString:withString: (etc) before you make your NSArray. Defense.


Excellent suggestion! Thanks.

----
Unrelated to my first post there ^ but given a string/line like the OP's: set UserName <User>

You can skip using NSScanner and do it sort of like this too..
    
NSRange range = [theString rangeOfString:@"<"];
NSString *result = [theString substringWithRange:NSMakeRange(range.location+range.length, [theString length]-range.location-range.length-1)];

NSLog(@"result: %@", result);


Not really all that different but shorter..though not after you add sanity checking, I guess. Carry on.

