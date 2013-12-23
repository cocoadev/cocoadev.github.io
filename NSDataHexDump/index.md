---
layout: page
title: NSDataHexDump
---

More than once I've needed to read data stored in NSData objects, and the default output of     -[NSData describe] is not really very human-readable. Thus, I created a small category to make traditional hex dumps from NSData objects, and an override for     describe to make it the default output format. The code should be self-explanatory, and can just be dropped into any project. Enjoy! - WAHa


    
@implementation NSData (HexDump)

-(NSString *)hexDumpWithColumns:(int)cols
{
	NSMutableString *str=[NSMutableString string];
	unsigned int len=[self length];
	const unsigned char *bytes=[self bytes];
	int lines=(len+cols-1)/cols;

	for(int i=0;i<lines;i++)
	{
		[str appendFormat:@"%08x   ",i*cols];

		for(int j=0;j<cols;j++)
		{
			int offs=i*cols+j;
			if(offs>=len) [str appendString:@"  "];
			else [str appendFormat:@"%02x",bytes[i*cols+j]];
			if(j%4==3&&j!=cols-1) [str appendString:@" "];
		}

		[str appendString:@"   "];

		for(int j=0;j<cols;j++)
		{
			int offs=i*cols+j;
			if(offs>=len) [str appendString:@" "];
			else
			{
				int c=bytes[i*cols+j];
				if(c<0x20||(c>=0x80&&c<0xa0)) c='.';
				[str appendFormat:@"%c",c];
			}
		}

		if(i!=lines-1) [str appendString:@"\n"];
	}
	return str;
}

-(NSString *)description { return [NSString stringWithFormat:@"<\n%@\n>",[self hexDumpWithColumns:16]]; }

@end


The output looks pretty much like this:

    
00000090   416e7441 656e756d 00000000 416e6e74   AntAenum....Annt
000000a0   00000000 416e536d 00000009 54657874   ....AnSm....Text
000000b0   496e6465 786c6f6e 67000000 04000000   Indexlong.......
000000c0   0a456e67 696e6544 61746174 64746100   .EngineDatatdta.
000000d0   0048650a 0a3c3c0a 092f456e 67696e65   .He..<<../Engine
000000e0   44696374 0a093c3c 0a09092f 45646974   Dict..<<.../Edit


----

see also: http://www.red-sweater.com/blog/279/hexy-little-thing

----

Hah, funny how that code ends up being structured nearly the same. I guess there is just the one obvious solution to this one -- WAHa

