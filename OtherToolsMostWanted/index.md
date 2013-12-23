---
layout: page
title: OtherToolsMostWanted
---

Features/fixes to existing non-Apple tools, or just tools you wish you had but don't have time to make (and maybe someone else will!)

----



* A fast, full-featured open source file browser. It'd probably never fully replace Finder, but it'd be nice to be able to view directories containing thousands of items without resorting to Terminal.app

* A CMM (Contextual Menu Module) that just puts the Services menu in the contextual menu. I know about ICECoFFEE, and have it installed, but I don't care about double-clicking on URLs and I just don't like having ApplicationEnhancer installed just for this. It seems like it'd be pretty easy to do - just grab the Services menu and stick it in there. But I lack CodeWarrior (you can't do CMMs with XCode, AFAIK)

* A command-line tool which opens an arbitrary dictionary plist, extracts the value for a given key, and gives it to stdout. Hoping someone else will make this. :-) Sample useage: plistvalue mydict.plist MyKeyName > storeItHere.txt

Ask and ye possibly may receive if someone gets bored enough. -- MikeFletcher

    
#import <Foundation/Foundation.h>

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    NSDictionary *myDict;
    id result;

    // Check arguments; print usage if we don't have a filename and
    // the key to print
    if( argc != 3 ) {
      [[NSFileHandle fileHandleWithStandardError]
        writeData:
          [[NSString stringWithFormat: @"usage: %s in.plist key\n",
                     argv[0]
            ] dataUsingEncoding: NSASCIIStringEncoding]];

      [pool release];
      exit( 1 );
    }

    // Ask NSDictionary to try and read plist file
    myDict = [NSDictionary dictionaryWithContentsOfFile:
                             [NSString stringWithCString: argv[1]]];

    // If it couldn't be read, gripe and bail
    if( myDict == nil ) {
      [[NSFileHandle fileHandleWithStandardError]
        writeData:
          [[NSString stringWithFormat:
                       @"Couldn't create dictionary from \"%s\"\n",
                     argv[1]
            ] dataUsingEncoding: NSASCIIStringEncoding]];

      [pool release];
      exit( 1 );
    }

    // try and pull out the requested key
    result = [myDict objectForKey: [NSString stringWithCString: argv[2]]];

    // If there's no such key, gripe and bail
    if( result == nil ) {
      [[NSFileHandle fileHandleWithStandardError]
        writeData:
          [[NSString stringWithFormat:
                       @"Couldn't retrieve value for key \"%s\"\n",
                     argv[2]
            ] dataUsingEncoding: NSASCIIStringEncoding]];

      [pool release];
      exit( 1 );
    }

    // Write requested value to stdout
    [[NSFileHandle fileHandleWithStandardOutput]
      writeData:
        [[NSString stringWithFormat: @"%@\n", result ]
          dataUsingEncoding: NSASCIIStringEncoding]];

    [pool release];
    return 0;
}

