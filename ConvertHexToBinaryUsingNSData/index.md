---
layout: page
title: ConvertHexToBinaryUsingNSData
---

This Objective-C category is rewritten from Erik Doernenburg's Foundation extensions: NSData(MIME). You can find his code in EDFrameworks' EDMessage Framework. You can use it to convert hex value that hold by NSData and convert it into binary value.

NSData+Hex.h

    
#import <Foundation/Foundation.h>

@interface NSData (NSData_Hex)
- (NSData*) hex2raw;
@end


NSData+Hex.m

    
#import "NSData+Hex.h"

static int asciitable[128] = {
  99,99,99,99, 99,99,99,99, 99,99,99,99, 99,99,99,99,
  99,99,99,99, 99,99,99,99, 99,99,99,99, 99,99,99,99,
  99,99,99,99, 99,99,99,99, 99,99,99,99, 99,99,99,99,
  0, 1, 2, 3,  4, 5, 6, 7,  8, 9, 99,99, 99,99,99,99, // 0..9
  99,10,11,12, 13,14,15,99, 99,99,99,99, 99,99,99,99, // A..F
  99,99,99,99, 99,99,99,99, 99,99,99,99, 99,99,99,99,
  99,10,11,12, 13,14,15,99, 99,99,99,99, 99,99,99,99, // a..f
  99,99,99,99, 99,99,99,99, 99,99,99,99, 99,99,99,99
};

@implementation NSData (NSData_Hex)

- (NSData*) hex2raw {
  // Based on Erik Doernenburg's NSData+MIME.m
  const char *source, *endOfSource;
  NSMutableData *decodedData;
  char *dest;
  
  source = [self bytes];
  endOfSource = source + [self length];
  decodedData = [NSMutableData dataWithLength:[self length]];
  dest = [decodedData mutableBytes];

  while (source < endOfSource) {
    if (isxdigit(*source) && isxdigit(*(source+1))) {
      *dest++ = asciitable[(int)*source] * 16 + asciitable[(int)*(source+1)];
      source += 2;   
    } else
      return Nil;
  }
  
  [decodedData setLength:(unsigned int)((void *)dest - [decodedData mutableBytes])];
  
  return decodedData;
}
@end


Reference: http://hollowout.blogspot.com/2009/01/convert-hex-value-to-binary-data.html

