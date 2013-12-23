---
layout: page
title: MDFive
---

What's the best (easiest) way to perform an MD5 hash of a string in Cocoa/Objective-C?

Thanks.

----

You can use openSSL to do it.

----

The MulleCipher framework has a category on NSData to create md5 digests; you can use NSString's -dataUsingEncoding: method to convert your string to data and then call that.  -- Bo

----

Enough with the stupid third party solutions already! This is using the libraries that come with OS X. I think it is a trivial mater to wrap the two lines (4-5 with error checking) into a category if you feel it is really necessary.
    
#include <openssl/md5.h>

NSData *data = [myString dataUsingEncoding:PickYourEncoding];
unsigned char *digest = MD5([data bytes], [data length], NULL);


Link in libcrypto.dynlib. Use your own buffer instead of using the static buffer if you need to be absolutely thread safe.

----

I wouldn't describe MulleCipher as "stupid"... it's actually a very nice framework. And in my case it proved educational in showing how to implement an MD5 algorithm from scratch (something I wanted to know how to do so I could obfuscate my methods and make my serial-number code harder to reverse engineer).


----

I tried the "native" Cocoa code above, but am having trouble getting my test results to work out.  From the shell, I get:

    
% md5 -s hello
MD5 ("hello") = 5d41402abc4b2a76b9719d911017c592


But I can't get this same result out of the unsigned char*.  Perhaps I am converting it incorrectly to an NSString?

    
NSString* test = [[NSString alloc] initWithString:@"hello"];
unsigned char* foo = [FileManager md5: [test dataUsingEncoding: NSASCIIStringEncoding]];
unsigned char x = foo[0];
NSLog(@"x is %C", x);


where md5 is a + method in a class I have called FileManager...defined just like the code above.

And it prints out "x is ]" which doesn't match "5" above.

----

Yes, it should match. The 5 you are trying to match is the high-nibble of a hex number that the command line utility is showing you. The first of 16 bytes (MD5_DIGEST_LENGTH). The character ']' == 0x5d ASCII.

This should clear things up...

    
#include <openssl/md5.h>
.
.
.
NSData *data = [@"hello" dataUsingEncoding:NSUTF8StringEncoding];
if (data) {
	NSMutableData *digest = [NSMutableData dataWithLength:MD5_DIGEST_LENGTH];
	if (digest && MD5([data bytes], [data length], [digest mutableBytes]))
		NSLog(@"%@", digest);
}


Which should emit in your log, <5d41402a bc4b2a76 b9719d91 1017c592 >, the MD5 digest in the "human readable" format you were expecting.

----

Great.  Thank you very much.  I was able to reproduce your results.  But now I'm tearing out my hair trying to get the MD5 into a normal NSString.  It seems to be an issue of being able to convert a "unsigned char" to "const char".  But I could be very wrong about that.

----

Hmm, something tells me I am reinventing the wheel here but the following can be used as a category addition to NSData to produce an NSString hex representation. A long winded sprintf or stringWithFormat could also do it for just the 16 bytes but this is more generic.

Remember, a MD5 digest is a 16 byte binary result, not a string.

    
static const char *const digits = "0123456789abcdef";

- (NSString*)hexadecimalRepresentation
{
	NSString *result = nil;
	size_t length = [self length];
	if (0 != length) {
		NSMutableData *temp = [NSMutableData dataWithLength:(length << 1)];
		if (temp) {
			const unsigned char *src = [self bytes];
			unsigned char *dst = [temp mutableBytes];
			if (src && dst) {
				while (length-- > 0) {
					*dst++ = digits[(*src >> 4) & 0x0f];
					*dst++ = digits[(*src++ & 0x0f)];
				}
				result = [[NSString alloc] initWithData:temp encoding:NSASCIIStringEncoding];
			}
		}
	}
	return (result) ? [result autorelease] : result;
}


----

Ah, stringWithFormat!  I didn't think of that.  Anyway, here's the long-winded version mentioned previously:
    
// toHash is an NSData
unsigned char* digest = MD5([toHash bytes], [toHash length], NULL);
NSString* s = [NSString stringWithFormat: @"%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x",
			digest[0], digest[1], 
			digest[2], digest[3],
			digest[4], digest[5],
			digest[6], digest[7],
			digest[8], digest[9],
			digest[10], digest[11],
			digest[12], digest[13],
			digest[14], digest[15]];


*The 02 prefix makes sure the hex values are zero-padded. --BenStiglitz*

*(My code was suddenly acting very strange...authentication failing randomly.  And then I noticed that a zero was missing from a hash and thought aha!  So I came back here and there was already a fix!  Thanks, Ben.  I think it would have taken me a very very long time to figure out the "02.")*

----

It might be safer, if a little slower, to do a loop:

    
// toHash is an NSData
unsigned char* digest = MD5([toHash bytes], [toHash length], NULL);
if (digest) {
	NSMutableString *ms = [NSMutableString string];
	for (int i = 0; i < MD5_DIGEST_LENGTH; i++) {
		[ms appendFormat: @"%02x", (int)(digest[i])];
	}
	NSString *s = ms copy] autorelease];
} else {
	/* digest failed */
	/* ... */
}

----
I was wondering if there was a way to do this not only with strings but all kinds of files, just like md5 in the shell does. Is there a way to pass the MD5() a path to a file and it "hashes" it for me? Would be awesome. thanks

----

toHash /* from the example above */ = [[[NSData dataWithContentsOfFile: yourPath];

Perhaps? :P

----
But note that this will not work with files larger than 2-3GB due to address space constraints. If you never plan on hashing files that large then you're fine. If you do, then you'll need to need to read the file piece by piece and use the MD5_Update function and its friends
----
Do you have some sample code on that? That would be awesome, thanks.
----
I'd suggest using the CC_MD5() function. It's basically the same as the openssl variant, but it's a wrapper created by Apple around the openssl library. If you use it, you won't have to link against openssl, and are protected against changes apple may make in the future (such as dropping openssl for another crypto library). 
----
Could someone tell me, how to use the code above as a method, so that I can call it with a NSString? I get some very strange Build Errors, when I try the Code above. (Newbie with Cocoa programming).
----
Leave the code the way it is and convert your string to a NSData

----

Just wanted to clarify to any users who want to use CC_MD5() such as myself.. you need to #import <CommonCrypto/CommonDigest.h> into your code, or else it will give a compiler warning of implicit function. No special frameworks or libs are needed to be included for this to work (besides maybe Foundation or Cocoa?), it should work right off the bat. -StevenDegutis

----

For the record, this is the code I've found is easiest to use CC_MD5():

    
- (NSString *) someMethodThatReturnsSomeHashForSomeString:(NSString*)concat {
	const char *concat_str = [concat UTF8String];
	unsigned char result[CC_MD5_DIGEST_LENGTH];
	CC_MD5(concat_str, strlen(concat_str), result);
	
	NSMutableString *hash = [NSMutableString string];
	
	for (int i = 0; i < 16; i++)
		[hash appendFormat:@"%02X", result[i]];
	
	return hash;
}


-StevenDegutis

----

Why not just do something like this:

    
...
hash = [NSMutableString stringWithFormat:@"%08x%08x%08x%08x", ((unsigned int *)&result)[0], ((unsigned int *)&result)[1], ((unsigned int *)&result)[2], ((unsigned int *)&result)[3]];
...


It seems to work for me, but you're going to want to watch the size of ints on your platform. You could use a safer type like uint32_t. It just seems to me that using a loop is unnecessary, but I guess this line is perhaps a little unreadable.

-DaltonMC

