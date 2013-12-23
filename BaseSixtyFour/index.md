---
layout: page
title: BaseSixtyFour
---



I wrote up a function to decode some Base64 text and return it. Here is the (somewhat messy) code I made:

    
 - (NSString *)decodeBase64:(NSString *)input {
    NSString *alphabet = @"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+-";
    NSString *decoded = @"";
    NSString *encoded = [input stringByPaddingToLength:(ceil([input length] / 4) * 4)
                                            withString:@"A"
                                       startingAtIndex:0];
    
    int i;
    char a, b, c, d;
    UInt32 z;
    
    for(i = 0; i < [encoded length]; i += 4) {
       a = [alphabet rangeOfString:[encoded substringWithRange:NSMakeRange(i + 0, 1)]].location;
       b = [alphabet rangeOfString:[encoded substringWithRange:NSMakeRange(i + 1, 1)]].location;
       c = [alphabet rangeOfString:[encoded substringWithRange:NSMakeRange(i + 2, 1)]].location;
       d = [alphabet rangeOfString:[encoded substringWithRange:NSMakeRange(i + 3, 1)]].location;
    	
       z = ((UInt32)a << 26) + ((UInt32)b << 20) + ((UInt32)c << 14) + ((UInt32)d << 8);
       decoded = [decoded stringByAppendingString:[NSString stringWithCString:(char *)&z]];
    }
 
 
    return decoded;
 }


It mostly works, but I'm looking for ways to improve it. Any suggestions on corners I could cut?

-- RyanGovostes

----

Most of the code examples I've seen that deal with Base64 data use mostly straight C/C++ code, not much Objective-C/Cocoa calls (slower, I assume). Maybe redo it using mostly straight C datatypes (char instead of NSString). Just a thought. --Kevin

----

Some advice:

*You should base64 decode into an NSData, not an NSString, since you want the existing structure to be 8-bit clean
*Lose all the object allocation; just malloc() a buffer up front of the proper size, write directly into it and then wrap it up in an NSData at the end.
*Error check your input for illegal characters.
*Reverse your lookup.  Create a lookup table that maps from your possible ASCII values to the corresponding 6-bit output values.
*Check for '=' characters, the official base64 padding character.  You're current implementation pads your output string with 0x00 characters, which is a no-no in an 8-bit clean world.

I'm sure I'll think of more but that's it for now ;) -- Bo

----

Here is a NSData category to do Base64 encode and decode, mostly C, used in Colloquy's ChatCore. ~ TimothyHatcher

http://project.colloquy.info/trac/file/trunk/NSDataAdditions.m

----

!!! BIG WARNING to those wanting to use the NSDataAdditions category (which has moved to http://colloquy.info/project/browser/trunk/Additions/NSDataAdditions.m ) !!!

The YouTube Plugin used by the UIWebView in iOS 4 uses a (hidden) method in NSData:      -(NSData*)base64Encoding. This obviously conflicts with     -(NSString*)base64Encoding defined by NSDataAdditions, will cause web pages with embedded YouTube links to crash your app. I learnt this from bitter personal experience.

I haven't checked the other methods, but consider yourselves warned.

- David Jacobson

----
Timothy: Is there any particular license on using NSDataAdditions?

*I believe it's safe to say it's public domain, since most people who publish categories on objects are just contributing info. If it were a 'custom solution' (like a fully-functional control, plug-in, or app), I would worry, but for any categories published here that doesn't say otherwise, consider it public domain. To be safe (and honest and honorable), you could and should mention the person's contribution to your own app in your credits.*

Another opinion: that category isn't published here, just linked from here.  The Colloquy project is GPLv2.  And the header itself says "Copyright (c) 2001 Kyle Hammond. All rights reserved.", Timothy just re-formatted it.

----
    
 [decoded stringByAppendingString:[NSString stringWithCString:(char *)&z]]


This line is detrimental to the performance of this method. Every time you call it, it copies 'decoded' plus z into a new string. So on the first call it copies 1 character, on the second it copies 2, on the third it copies 3, on the fourth it copies 4 ... It make the method run in n^2 time. The method will run much faster with large inputs if you put the output buffer into an NSMutableData - Jon H.

----
I'm attempting to use the NSDataAdditions from Colloquy, and having some problems decoding.  To encode, I do:
    
	NSData *data = [NSData dataWithContentsOfFile:filePath];
	NSString *encodedFile = [data base64Encoding];
	char *cString = [encodedFile UTF8String];

With a file that has contents like:
    
 blahblahblah
 short file
 blah
 hi there.

And then send the data on it's way.  When the receiving application receives the encoded data, it's encoded as:
 YmxhaGJsYWhibGFoCnNob3J0IGZpbGUKYmxhaApoaSB0aGVyZS4=
which is correct, confirmed by running the following command at the command line:
    
 echo YmxhaGJsYWhibGFoCnNob3J0IGZpbGUKYmxhaApoaSB0aGVyZS4= |openssl base64 -d

I decode the data:
    
	NSData *fileData = [NSData dataWithBase64EncodedString:base64String];
	NSString *blah = [NSString stringWithCharacters:[fileData bytes] length:[fileData length]];
	[fileData writeToFile:[filePath stringByAppendingPathComponent:fileName]
			   atomically:YES];

base64String is still correct at this point.

The output is:
    
 blbhbhahalal
 sJoro f`le,blbh
 hi ihehe.

Which clearly has been munged.  So the question: am I doing something wrong decoding?


if you do     -[NSString UTF8String], shouldn't you also do     +[NSString stringWithUTF8String]>/code> ?

----

You can decode and encode Base64 via the command line:
    
 cat inputFile.in | openssl base64 -d >> outputFile.out

Use "-e" for encoding, "-d" for decoding.

----

Here is an NSData category  method based on the command above that converts an NSData object to a base64 string:
    
 - (NSString *)base64Encoding
 {
 	NSTask *task = [NSTask alloc] init] autorelease];
 	NSPipe *inPipe = [NSPipe pipe], *outPipe = [NSPipe pipe];
 	NSFileHandle *inHandle = [inPipe fileHandleForWriting], *outHandle = [outPipe fileHandleForReading];
 	NSData *outData = nil;
 	
 	[task setLaunchPath:@"/usr/bin/openssl"];
 	[task setArguments:[NSArray arrayWithObjects:@"base64", @"-e", nil;
 	[task setStandardInput:inPipe];
 	[task setStandardOutput:outPipe];
 	[task setStandardError:outPipe];
 	
 	[task launch];
 	
 	[inHandle writeData:self];
 	[inHandle closeFile];
 
 	[task waitUntilExit];
 	
 	outData = [outHandle readDataToEndOfFile];
 	if (outData)
 	{
 		NSString *base64 = [NSString alloc] initWithData:outData encoding:NSUTF8StringEncoding] autorelease];
 		if (base64)
 			return base64;
 	}
 	
 	return nil;
 }

----
I find that the best way to implement this sort of thing is, well, to not implement it myself, but find an open-source project that already has, and use that.  A library like libgsasl ( http://josefsson.org/gsasl/ ) is almost guaranteed to be implemented more completely and perform better than what you brew up. Granted, it's straight C, but it's really not hard to use (particularly for something simple like base64 encoding/decoding).  Just my 2 cents.  --[[NoJo

----

You can using libcrypto, part of OpenSSL, to do this directly without spawning a task:

    
 #include <openssl/bio.h>
 #include <openssl/evp.h>
 
 @interface NSData (Base64)
 
 - (NSString *) encodeBase64;
 - (NSString *) encodeBase64WithNewlines: (BOOL) encodeWithNewlines;
 
 @end
 
 @implementation NSData (Base64)
 
 - (NSString *) encodeBase64;
 {
     return [self encodeBase64WithNewlines: YES];
 }
 
 - (NSString *) encodeBase64WithNewlines: (BOOL) encodeWithNewlines;
 {
     // Create a memory buffer which will contain the Base64 encoded string
     BIO * mem = BIO_new(BIO_s_mem());
     
     // Push on a Base64 filter so that writing to the buffer encodes the data
     BIO * b64 = BIO_new(BIO_f_base64());
     if (!encodeWithNewlines)
         BIO_set_flags(b64, BIO_FLAGS_BASE64_NO_NL);
     mem = BIO_push(b64, mem);
     
     // Encode all the data
     BIO_write(mem, [self bytes], [self length]);
     BIO_flush(mem);
     
     // Create a new string from the data in the memory buffer
     char * base64Pointer;
     long base64Length = BIO_get_mem_data(mem, &base64Pointer);
     NSString * base64String = [NSString stringWithCString: base64Pointer
                                                    length: base64Length];
     
     // Clean up and go home
     BIO_free_all(mem);
     return base64String;
 }
 
 @end


You need to link against     /usr/lib/libcrypto.dyld.  I've tested on OS X 10.4.5. It does seem to build and run if I use the 10.2.8 SDK, which would imply that OpenSSL is included all the way back to at least 10.2.  Here's the man page which shows how you could decode a Base64 string into data, as well:

http://developer.apple.com/documentation/Darwin/Reference/ManPages/man3/BIO_f_base64.3ssl.html

-- DaveDribin

----
WARNING: the base64Pointer is NOT a string C pointer !!!
You must update the source like this:
    
 // Create a new string from the data in the memory buffer
 char * base64Pointer;
 long base64Length = BIO_get_mem_data(mem, &base64Pointer);
 
 // The base64Pointer is NOT null terminated
 NSData * base64data = [NSData dataWithBytesNoCopy:base64Pointer length:base64Length freeWhenDone:NO];
 NSString * base64String = NSString alloc] initWithData:base64data encoding:NSUTF8StringEncoding];


-- JM Marino

----

Okay, here's code to decode with libssl:

    
 #include <openssl/bio.h>
 #include <openssl/evp.h>
 
 @interface NSString (Base64)
 
 - (NSData *) decodeBase64;
 - (NSData *) decodeBase64WithNewlines: (BOOL) encodedWithNewlines;
 
 @end
 
 @implementation NSString (Base64)
 
 - (NSData *) decodeBase64;
 {
     return [self decodeBase64WithNewlines: YES];
 }
 
 - (NSData *) decodeBase64WithNewlines: (BOOL) encodedWithNewlines;
 {
     // Create a memory buffer containing Base64 encoded string data
     BIO * mem = BIO_new_mem_buf((void *) [self cString], [self cStringLength]);
     
     // Push a Base64 filter so that reading from the buffer decodes it
     BIO * b64 = BIO_new(BIO_f_base64());
     if (!encodedWithNewlines)
         BIO_set_flags(b64, BIO_FLAGS_BASE64_NO_NL);
     mem = BIO_push(b64, mem);
     
     // Decode into an NSMutableData
     NSMutableData * data = [NSMutableData data];
     char inbuf[512];
     int inlen;
     while ((inlen = BIO_read(mem, inbuf, sizeof(inbuf))) > 0)
         [data appendBytes: inbuf length: inlen];
     
     // Clean up and go home
     BIO_free_all(mem);
     return data;
 }
 
 @end


-- [[DaveDribin

----

Here's an MIT licensed Xcode 2.2 project with the encoding and decoding categories, along with unit tests: http://www.dribin.org/dave/files/base64.zip

-- DaveDribin

----

Here's a simple category for this which doesn't require libcrypto:

    
 @interface NSData (MBBase64)
 
 + (id)dataWithBase64EncodedString:(NSString *)string;     //  Padding '=' characters are optional. Whitespace is ignored.
 - (NSString *)base64Encoding;
 @end
 
 
 static const char encodingTable[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
 
 
 @implementation NSData (MBBase64)
 
 + (id)dataWithBase64EncodedString:(NSString *)string;
 {
 	if (string == nil)
 		[NSException raise:NSInvalidArgumentException format:nil];
 	if ([string length] == 0)
 		return [NSData data];
 	
 	static char *decodingTable = NULL;
 	if (decodingTable == NULL)
 	{
 		decodingTable = malloc(256);
 		if (decodingTable == NULL)
 			return nil;
 		memset(decodingTable, CHAR_MAX, 256);
 		NSUInteger i;
 		for (i = 0; i < 64; i++)
 			decodingTable[(short)encodingTable[i]] = i;
 	}
 	
 	const char *characters = [string cStringUsingEncoding:NSASCIIStringEncoding];
 	if (characters == NULL)     //  Not an ASCII string!
 		return nil;
 	char *bytes = malloc((([string length] + 3) / 4) * 3);
 	if (bytes == NULL)
 		return nil;
 	NSUInteger length = 0;
 
 	NSUInteger i = 0;
 	while (YES)
 	{
 		char buffer[4];
 		short bufferLength;
 		for (bufferLength = 0; bufferLength < 4; i++)
 		{
 			if (characters[i] == '\0')
 				break;
 			if (isspace(characters[i]) || characters[i] == '=')
 				continue;
 			buffer[bufferLength] = decodingTable[(short)characters[i]];
 			if (buffer[bufferLength++] == CHAR_MAX)      //  Illegal character!
 			{
 				free(bytes);
 				return nil;
 			}
 		}
 		
 		if (bufferLength == 0)
 			break;
 		if (bufferLength == 1)      //  At least two characters are needed to produce one byte!
 		{
 			free(bytes);
 			return nil;
 		}
 		
 		//  Decode the characters in the buffer to bytes.
 		bytes[length++] = (buffer[0] << 2) | (buffer[1] >> 4);
 		if (bufferLength > 2)
 			bytes[length++] = (buffer[1] << 4) | (buffer[2] >> 2);
 		if (bufferLength > 3)
 			bytes[length++] = (buffer[2] << 6) | buffer[3];
 	}
 	
 	realloc(bytes, length);
 	return [NSData dataWithBytesNoCopy:bytes length:length];
 }
 
 - (NSString *)base64Encoding;
 {
 	if ([self length] == 0)
 		return @"";
 
  char *characters = malloc((([self length] + 2) / 3) * 4);
 	if (characters == NULL)
 		return nil;
 	NSUInteger length = 0;
 	
 	NSUInteger i = 0;
 	while (i < [self length])
 	{
 		char buffer[3] = {0,0,0};
 		short bufferLength = 0;
 		while (bufferLength < 3 && i < [self length])
 			buffer[bufferLength++] = ((char *)[self bytes])[i++];
 		
 		//  Encode the bytes in the buffer to four characters, including padding "=" characters if necessary.
 		characters[length++] = encodingTable[(buffer[0] & 0xFC) >> 2];
 		characters[length++] = encodingTable[((buffer[0] & 0x03) << 4) | ((buffer[1] & 0xF0) >> 4)];
 		if (bufferLength > 1)
 			characters[length++] = encodingTable[((buffer[1] & 0x0F) << 2) | ((buffer[2] & 0xC0) >> 6)];
 		else characters[length++] = '=';
 		if (bufferLength > 2)
 			characters[length++] = encodingTable[buffer[2] & 0x3F];
 		else characters[length++] = '=';	
 	}
 	
 	return [NSString alloc] initWithBytesNoCopy:characters length:length encoding:NSASCIIStringEncoding freeWhenDone:YES] autorelease];
 }
 
 @end


-- [[MiloBird

----

MiloBird - what is the license for your base64 category for this which doesn't require libcrypto?

-- Susheel

----

It's public domain, just use it. Nothing fancy there, just thought I'd share as I for one found it sufficiently annoying to have to roll my own.

-- MiloBird

----

MiloBird - is there a way I can privately message you in regards to the use of this code?

-- Sapchiquita

----

Here's a version that's very simple and clean and doesn't require libcrypto.  Works great on OSX or the iPhone.  Public domain.

    
 #define ArrayLength(x) (sizeof(x)/sizeof(*(x)))
 
 static char encodingTable[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
 static char decodingTable[128];
 
 + (void) initialize {
   if (self == [Base64 class]) {
     memset(decodingTable, 0, ArrayLength(decodingTable));
     for (NSInteger i = 0; i < ArrayLength(encodingTable); i++) {
       decodingTable[encodingTable[i]] = i;
     }
   }
 }
 
 
 + (NSString*) encode:(const uint8_t*) input length:(NSInteger) length {
     NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
     uint8_t* output = (uint8_t*)data.mutableBytes;
 
     for (NSInteger i = 0; i < length; i += 3) {
         NSInteger value = 0;
         for (NSInteger j = i; j < (i + 3); j++) {
             value <<= 8;
 
             if (j < length) {
                 value |= (0xFF & input[j]);
             }
         }
 
         NSInteger index = (i / 3) * 4;
         output[index + 0] =                    encodingTable[(value >> 18) & 0x3F];
         output[index + 1] =                    encodingTable[(value >> 12) & 0x3F];
         output[index + 2] = (i + 1) < length ? encodingTable[(value >> 6)  & 0x3F] : '=';
         output[index + 3] = (i + 2) < length ? encodingTable[(value >> 0)  & 0x3F] : '=';
     }
 
     return [NSString alloc] initWithData:data
                                   encoding:NSASCIIStringEncoding] autorelease];
 }
 
 
 + (NSString*) encode:(NSData*) rawBytes {
     return [self encode:(const uint8_t*) rawBytes.bytes length:rawBytes.length];
 }
 
 
 + (NSData*) decode:(const char*) string length:(NSInteger) inputLength {
   if ((string == NULL) || (inputLength % 4 != 0)) {
     return nil;
   }
   
   while (inputLength > 0 && string[inputLength - 1] == '=') {
     inputLength--;
   }
 
   NSInteger outputLength = inputLength * 3 / 4;
   NSMutableData* data = [NSMutableData dataWithLength:outputLength];
   uint8_t* output = data.mutableBytes;
   
   NSInteger inputPoint = 0;
   NSInteger outputPoint = 0;
   while (inputPoint < inputLength) {
     char i0 = string[inputPoint++];
     char i1 = string[inputPoint++];
     char i2 = inputPoint < inputLength ? string[inputPoint++] : 'A'; /* 'A' will decode to \0 */
     char i3 = inputPoint < inputLength ? string[inputPoint++] : 'A';
 
     output[outputPoint++] = (decodingTable[i0] << 2) | (decodingTable[i1] >> 4);
     if (outputPoint < outputLength) {
       output[outputPoint++] = ((decodingTable[i1] & 0xf) << 4) | (decodingTable[i2] >> 2);
     }
     if (outputPoint < outputLength) {
       output[outputPoint++] = ((decodingTable[i2] & 0x3) << 6) | decodingTable[i3];
     }
   }
   
   return data;
 }
 
 
 + (NSData*) decode:(NSString*) string {
   return [self decode:[string cStringUsingEncoding:NSASCIIStringEncoding] length:string.length];
 }


-- Cyrus

----

A really, really fast implementation which was ported (and modified/improved) from the PHP Core library into native Objective-C code is available in the **[[QSStrings Class** from the **QSUtilities Library**.  I did a quick benchmark: a 5.3MB image (JPEG) file took < 50ms to encode, and about 140ms to decode.

The code for the entire library (including the Base64 Methods) are available on GitHub at **https://github.com/mikeho/QSUtilities**.

Or alternatively, if you want the code to *just* the Base64 methods themselves, I've posted it here:

    
 static const char _base64EncodingTable[64] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
 static const short _base64DecodingTable[256] = {
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -1, -1, -2, -1, -1, -2, -2,
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2,
 	-1, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, 62, -2, -2, -2, 63,
 	52, 53, 54, 55, 56, 57, 58, 59, 60, 61, -2, -2, -2, -2, -2, -2,
 	-2,  0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14,
 	15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, -2, -2, -2, -2, -2,
 	-2, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40,
 	41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, -2, -2, -2, -2, -2,
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2,
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2,
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2,
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2,
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2,
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2,
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2,
 	-2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2, -2
 };
 
 + (NSString *)encodeBase64WithString:(NSString *)strData {
 	return [QSStrings encodeBase64WithData:[strData dataUsingEncoding:NSUTF8StringEncoding]];
 }
 
 + (NSString *)encodeBase64WithData:(NSData *)objData {
 	const unsigned char * objRawData = [objData bytes];
 	char * objPointer;
 	char * strResult;
 
 	// Get the Raw Data length and ensure we actually have data
 	int intLength = [objData length];
 	if (intLength == 0) return nil;
       
 	// Setup the String-based Result placeholder and pointer within that placeholder
 	strResult = (char *)calloc(((intLength + 2) / 3) * 4, sizeof(char));
 	objPointer = strResult;
 	
 	// Iterate through everything
         
 	while (intLength > 2) { // keep going until we have less than 24 bits
 		*objPointer++ = _base64EncodingTable[objRawData[0] >> 2];
 		*objPointer++ = _base64EncodingTable[((objRawData[0] & 0x03) << 4) + (objRawData[1] >> 4)];
 		*objPointer++ = _base64EncodingTable[((objRawData[1] & 0x0f) << 2) + (objRawData[2] >> 6)];
 		*objPointer++ = _base64EncodingTable[objRawData[2] & 0x3f];
 		
 		// we just handled 3 octets (24 bits) of data
 		objRawData += 3;
 		intLength -= 3; 
 	}
 
 	// now deal with the tail end of things
 	if (intLength != 0) {
 		*objPointer++ = _base64EncodingTable[objRawData[0] >> 2];
 		if (intLength > 1) {
 			*objPointer++ = _base64EncodingTable[((objRawData[0] & 0x03) << 4) + (objRawData[1] >> 4)];
 			*objPointer++ = _base64EncodingTable[(objRawData[1] & 0x0f) << 2];
 			*objPointer++ = '=';
 		} else {
 			*objPointer++ = _base64EncodingTable[(objRawData[0] & 0x03) << 4];
 			*objPointer++ = '=';
 			*objPointer++ = '=';
 		}
 	}
 
 	// Terminate the string-based result
 	*objPointer = '\0';
 
 	// Return the results as an NSString object
 	return [NSString stringWithCString:strResult encoding:NSASCIIStringEncoding];
 }
 
 + (NSData *)decodeBase64WithString:(NSString *)strBase64 {
 	const char * objPointer = [strBase64 cStringUsingEncoding:NSASCIIStringEncoding];
 	int intLength = strlen(objPointer);
 	int intCurrent;
 	int i = 0, j = 0, k;
 
 	unsigned char * objResult;
 	objResult = calloc(intLength, sizeof(char));
 
 	// Run through the whole string, converting as we go
 	while ( ((intCurrent = *objPointer++) != '\0') && (intLength-- > 0) ) {
 		if (intCurrent == '=') {
 			if (*objPointer != '=' && ((i % 4) == 1)) {// || (intLength > 0)) {
 				// the padding character is invalid at this point -- so this entire string is invalid
 				free(objResult);
 				return nil;
 			}
 			continue;
 		}
 
 		intCurrent = _base64DecodingTable[intCurrent];
 		if (intCurrent == -1) {
 			// we're at a whitespace -- simply skip over
 			continue;
 		} else if (intCurrent == -2) {
 			// we're at an invalid character
 			free(objResult);
 			return nil;
 		}
 
 		switch (i % 4) {
 			case 0:
 				objResult[j] = intCurrent << 2;
 				break;
 
 			case 1:
 				objResult[j++] |= intCurrent >> 4;
 				objResult[j] = (intCurrent & 0x0f) << 4;
 				break;
 
 			case 2:
 				objResult[j++] |= intCurrent >>2;
 				objResult[j] = (intCurrent & 0x03) << 6;
 				break;
 
 			case 3:
 				objResult[j++] |= intCurrent;
 				break;
 		}
 		i++;
 	}
 
 	// mop things up if we ended on a boundary
 	k = j;
 	if (intCurrent == '=') {
 		switch (i % 4) {
 			case 1:
 				// Invalid state
 				free(objResult);
 				return nil;
 
 			case 2:
 				k++;
 				// flow through
 			case 3:
 				objResult[k] = 0;
 		}
 	}
 
 	// Cleanup and setup the return NSData
 	NSData * objData = [NSData alloc] initWithBytes:objResult length:j] autorelease];
 	free(objResult);
 	return objData;
 }



For any questions, issues, etc., please feel free and post on the comments in [[GitHub at https://github.com/mikeho/QSUtilities.


----
I wanted to be able to encode and decode an NSString both ways. Example here:
http://agerson.net/base64-encode-and-decode-nsstring-cocoa

----
Several categories use a construct calling realloc like so:
    
 realloc(bytes, smallerSize)
 return [NSData dataWithBytesNoCopy:bytes];

But this has a potential error (I think). realloc is supposed to do inplace modification of a pointer if the new size is smaller than the original malloc. Visit  for more info .But it seems that rarely (I think when malloc is busy, and wants to compact?) it actually changes the pointer, to that you should called bytes = realloc(bytes, smallerSize). Or perhaps it only happens with debug malloc on. Either way its better to be safe. I was (I think) running into this when calling on multiple threads, with heavy C++ allocation/deallocation  going on in other threads.  


One way to see this is to run it with the 'Enable Guard Malloc' set in the XCode run menu. It will then trip up often. So I guess its dangerous to simply call realloc(bytes, size) - you should always assign. 
Turn on Malloc guarding, then try this:
    
 char* oldPtr = bytes;
 bytes = realloc(bytes, smallerSize);
 if (bytes != oldPtr)
     NSLog(@"Found case where realloc changed the address of a pointer");
 return [NSData dataWithBytesNoCopy:bytes];


Here is my code: https://github.com/sprhawk/ytoolkit/blob/master/ybase64/code/ybase64.c

with Cocoa additions to NSString/NSData: https://github.com/sprhawk/ytoolkit/tree/master/ybase64additions/code

This is the benchmark, compared to NSData+Base64/GNUCoreUtiles/libb64: https://github.com/sprhawk/ytoolkit/blob/master/BENCHMARK

