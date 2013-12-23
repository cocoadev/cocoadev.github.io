---
layout: page
title: NSDataPlusBzip
---

As a (small) return to the community, here's a category that adds bzip2 compression to NSData. It requires adding libbz2.a to the project and uses Objective-C's modern exception style.

I'll be adding an NSValueTransformer soon. Done. See BzipValueTransformer � BryanWoods

    
// NSData+Bzip2.h
@interface NSData (Bzip2)

- (NSData *) bzip2;
- (NSData *) bzip2WithCompressionSetting:(int)OneToNine;
- (NSData *) bunzip2;

@end


// NSData+Bzip2.m
#import "NSData+Bzip2.h"
#import "bzlib.h"

@implementation NSData (Bzip2)

- (NSData *) bzip2WithCompressionSetting:(int)compression
{
	int bzret, buffer_size = 1000000;
	bz_stream stream = { 0 };
	stream.next_in = [self bytes];
	stream.avail_in = [self length];
	
	NSMutableData * buffer = [NSMutableData dataWithLength:buffer_size];
	stream.next_out = [buffer mutableBytes];
	stream.avail_out = buffer_size;
	
	NSMutableData * compressed = [NSMutableData data];
	
	BZ2_bzCompressInit(&stream, compression, 0, 0);
	@try {
		do {
			bzret = BZ2_bzCompress(&stream, (stream.avail_in) ? BZ_RUN : BZ_FINISH);
			if (bzret != BZ_RUN_OK && bzret != BZ_STREAM_END)
				@throw [NSException exceptionWithName:@"bzip2" reason:@"BZ2_bzCompress failed" userInfo:nil];

			[compressed appendBytes:[buffer bytes] length:(buffer_size - stream.avail_out)];
			stream.next_out = [buffer mutableBytes];
			stream.avail_out = buffer_size;
		} while(bzret != BZ_STREAM_END);
	}
	@finally {
		BZ2_bzCompressEnd(&stream);
	}
	
	return compressed;
}

- (NSData *) bunzip2
{
	int bzret;
	bz_stream stream = { 0 };
	stream.next_in = [self bytes];
	stream.avail_in = [self length];
	
	const int buffer_size = 10000;
	NSMutableData * buffer = [NSMutableData dataWithLength:buffer_size];
	stream.next_out = [buffer mutableBytes];
	stream.avail_out = buffer_size;
	
	NSMutableData * decompressed = [NSMutableData data];
	
	BZ2_bzDecompressInit(&stream, 0, NO);
	@try {
		do {
			bzret = BZ2_bzDecompress(&stream);
			if (bzret != BZ_OK && bzret != BZ_STREAM_END)
				@throw [NSException exceptionWithName:@"bzip2" reason:@"BZ2_bzDecompress failed" userInfo:nil];

			[decompressed appendBytes:[buffer bytes] length:(buffer_size - stream.avail_out)];
			stream.next_out = [buffer mutableBytes];
			stream.avail_out = buffer_size;
		} while(bzret != BZ_STREAM_END);
	}
	@finally {
		BZ2_bzDecompressEnd(&stream);
	}
	
	return decompressed;
}
@end

