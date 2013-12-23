---
layout: page
title: TroubleWithNSScanner
---

Hi, I am trying to scan a text file using nsscanner. but it failed somehow and i don't know why.

here is the file example : 
    
v -0.38733 14.14545 -0.06502
v 0.19083 14.34016 -0.98115
v -0.50296 14.58355 -2.12632
v -2.12179 14.63222 -2.35535
v -2.69994 14.38883 -1.21019


vn -0.01873 -0.01664 -0.01477
vn -0.00510 -0.00352 -0.00479
vn -0.00198 -0.00065 -0.00220
vn -0.00568 -0.00643 -0.00543
vn -0.01920 -0.02015 -0.01348
vn -0.02511 -0.02719 -0.01687
vn -0.01049 0.00405 0.00145
vn -0.00699 0.00251 0.01230
vn 0.00175 0.00160 0.01005
vn -0.00874 -0.00243 -0.00257
g STEM
f 3 2 1 
f 2 4 1 
f 4 5 1 
f 6 1 5 
f 5 7 6 
f 7 6 8 
f 8 9 6 
f 8 10 9 
f 10 11 9


and here is my code : 
    
 // this is the filename -> objFileName

General/NSString *objFile = General/[NSString stringWithContentsOfFile:objFileName];
General/NSScanner *scanner;

float x,y,z;
scanner = General/[NSScanner scannerWithString:objFile];

while (![scanner isAtEnd])
{
   if ([scanner scanString:@"v" intoString:nil])
   {
      [scanner scanFloat:&x];
      [scanner scanFloat:&y];
      [scanner scanFloat:&z];
   }
   General/NSLog(@"x = %f , y = %f , z = %f", x,y,z);
}


this code end up with displaying all the "x = <blah> , y = <blah> , z = <blah>" but then my application freeze up. Thanks in advance - General/JuliusJuarsa

*Where does it freeze?*

I think it was right after the application displays all the v and then it try to scan the next line i guest;
but then i try to add few of these line  between the while iteration :
    
   if .....
   }
   else if ([scanner scanString:@"vn" intoString:nil])
   {
      [scanner scanFloat:&x];
      [scanner scanFloat:&y];
      [scanner scanFloat:&z];
   }
   General/NSLog(@"x = %f , y = %f , z = %f", x,y,z);
}


the run log window shows nothing T.T - General/JuliusJuarsa

*"I think" is a bad thing here. Run your program in the debugger and find out exactly where it's getting stuck.*

----

thanks for the tip on using the debugger. Still my first time using it though but it's proven to be usefull ^^, i've found the problem. it stuck because when the scanner didn't find any of the 'v','vn' or 'f' i didn't scan for the newLineCharacterSet. here is the final code :

    
	General/NSString *objFile = General/[NSString stringWithContentsOfFile:objFileName];

	General/NSScanner *scanner;
	General/NSCharacterSet *newLineCharacterSet = General/[NSCharacterSet characterSetWithCharactersInString:@"\n"];
		
	float x,y,z;
	int jmhTitik = 0;
	int jmhVn = 0;
	int jmhF = 0;
	scanner = General/[NSScanner scannerWithString:objFile];

	while ([scanner isAtEnd] == NO)
	{
		if ([scanner scanString:@"v " intoString:nil])
		{
			[scanner scanFloat:&x];
			[scanner scanFloat:&y];
			[scanner scanFloat:&z];

			jmhTitik++;
		} 
		else if ([scanner scanString:@"vn " intoString:nil])
		{
			[scanner scanFloat:&x];
			[scanner scanFloat:&y];
			[scanner scanFloat:&z];
			jmhVn++;
		}
		else if ([scanner scanString:@"f " intoString:nil])
		{
			[scanner scanInt:nil];
			[scanner scanInt:nil];
			[scanner scanInt:nil];
			jmhF++;
		} 
		else
		{
			[scanner scanUpToCharactersFromSet:newLineCharacterSet intoString:nil];
		}
	}
		
	General/NSLog(@"jumlah titik = %d ; jumlah vn = %d ; jumlah f = %d",jmhTitik,jmhVn,jmhF);


----

I am trying to use General/NSScanner on some malformed HTML but immediately ran into problems. Here is some General/SenTestingKit code to illustrate the problem.

    
@implementation General/MyScanner

- (void)test_MyScanner
{
    General/NSString  *row  = nil;
    General/NSScanner *html = General/[NSScanner scannerWithString:@"<html><body><tr><td>index 0</td></tr></body></html>"];
    
    /* Capture @"<tr><td>index 0</td></tr>" in variable row */
    
    [html scanUpToString:@"<tr>" intoString:nil];
    General/STAssertTrue( ([html scanLocation] == 12), @"(1) scanner @ %d", [html scanLocation] );

    [html scanString:@"</tr>" intoString:&row];
    General/STAssertTrue( ([html scanLocation] == 37), @"(2) scanner @ %d", [html scanLocation] );
}
@end


This code fails with the following assertion print.

<code>
error: -General/[MyScanner test_MyScanner]: "([html scanLocation] == 37)" should be true, (2) scanner @ 12
</code>

Repeating with the following also fails.

    
@implementation General/MyScanner

- (void)test_MyScanner
{
    General/NSString  *row  = nil;
    General/NSScanner *html = General/[NSScanner scannerWithString:@"<html><body><tr><td>index 0</td></tr></body></html>"];
    
    /* Just try to advance the scanner to after </tr> */

    [html scanString:@"</tr>" intoString:nil];
    General/STAssertTrue( ([html scanLocation] == 37), @"(2) scanner @ %d", [html scanLocation] );
}
@end


This code fails with the following assertion print.

<code>
error: -General/[MyScanner test_MyScanner]: "([html scanLocation] == 37)" should be true, (2) scanner @ 0
</code>

Using only scanUpToString for both tags works but I can't use the General/NSString* returned in the intoString argument.  I have to use math to advance the cursor past the trailing tag and then an General/NSString method to actual extract the subrange.  What am I missing?  Why can't I use the first code snippet?

----
Use both.     scanUpToString: followed by     scanString: for both tags.     scanString: will not move the scan location, so the string you scan must be at the current location. Likewise,     scanUpToString: will not move the location past the string, so you have to advance past it by actually scanning the string as well.
