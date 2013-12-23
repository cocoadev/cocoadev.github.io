---
layout: page
title: NSScannerTimeTrialSourceCode
---

    
 #import <Cocoa/Cocoa.h>
 #import <time.h>
 
 int main(void) {
   NSAutoreleasePool *pool = [NSAutoreleasePool new];
   
   NSFileHandle *stdinHandle = [NSFileHandle fileHandleWithStandardInput];
   NSLog(@"reading...");
   NSData *data = [stdinHandle readDataToEndOfFile];
   NSString *str = [[[NSString alloc] initWithData:data encoding:NSMacOSRomanStringEncoding] autorelease];
   
   NSScanner *scanner = [NSScanner scannerWithString:str];
   NSString *qString = @"q";
   NSCharacterSet *qCharSet = [NSCharacterSet characterSetWithCharactersInString:qString];
   
   clock_t before, after;
   double delta;
   
   NSLog(@"scanning...");
   
   before = clock();
   while([scanner scanUpToCharactersFromSet:qCharSet intoString:nil])
     [scanner scanCharactersFromSet:qCharSet intoString:nil];
   after = clock();
   delta  = after;
   delta -= before;
   delta /= CLOCKS_PER_SEC;
   NSLog(@"with character set: %f", delta);
   
   //rewind
   [scanner setScanLocation:0U];
   
   before = clock();
   while([scanner scanUpToString:qString intoString:nil])
     [scanner scanString:qString intoString:nil];
   after = clock();
   delta  = after;
   delta -= before;
   delta /= CLOCKS_PER_SEC;
   NSLog(@"with string: %f", delta);
   
   //rewind
   [scanner setScanLocation:0U];
   
   //I added a second charset test to be sure my results (see below) weren't the result of caching. --boredzo
   before = clock();
   while([scanner scanUpToCharactersFromSet:qCharSet intoString:nil])
     [scanner scanCharactersFromSet:qCharSet intoString:nil];
   after = clock();
   delta  = after;
   delta -= before;
   delta /= CLOCKS_PER_SEC;
   NSLog(@"with character set: %f", delta);
   
   [pool release];
   return 0;
 }


----

on a 450 M'Hz MPC7400 (G4 Cube) running 10.3.5:

    
 make scan                                                 %/Volumes/RAM Disk(0)
 gcc scan.m -framework Cocoa -o scan -std=c99
 ___
 du -h ~/Documents/big-headers.txt                         %/Volumes/RAM Disk(0)
 36M     ~/Documents/big-headers.txt
 ___
 scan < ~/Documents/big-headers.txt                        %/Volumes/RAM Disk(0)
 2004-09-17 11:28:17.773 scan[1358] reading...
 2004-09-17 11:28:18.586 scan[1358] scanning...
 2004-09-17 11:28:29.292 scan[1358] with character set: 8.710000
 2004-09-17 11:28:30.752 scan[1358] with string: 1.200000
 2004-09-17 11:28:42.678 scan[1358] with character set: 8.610000


*--boredzo*

