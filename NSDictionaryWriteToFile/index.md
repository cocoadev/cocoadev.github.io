---
layout: page
title: NSDictionaryWriteToFile
---


I'm not sure what I am doing wrong but, the NSMutableDictionary I created won't write to file.  When I tried wrote the description to file using NSString, it seemed to work though.  

    
- (void)saveToFile: (NSString *)fileName
{
	NSLog(@"%@", [profileList description]); // This Shows Up Fine
	profileList description] writeToFile:@"List.xml" atomically:YES]; // This saves fine
	[[[[NSDictionary dictionaryWithDictionary:profileList] writeToFile:fileName atomically:YES]; // This just doesn't work!
}


Can anyone give me some ideas?

Thanks

----

If your dictionary contains objects that aren't standard property list items (NSNumber, NSString, NSDictionary, NSArray, NSData, NSDate) then it won't write out to file. The proper way to write a dictionary is:
    
[profileList writeToFile:fileName atomically:YES];

but only if you don't have any custom classes or stuff in there...

If you have custom classes, then you can write them to a file by making them all conform to NSCoding, and then using NSKeyedArchiver to transform them to NSData.

----

Your code works just fine.
I suggest you look at permissions for the file you are trying to create - check the path and make sure the user running the application has the write to create the file.

FYI, here's the test code:

    
#import <Cocoa/Cocoa.h>
@interface MyObject: NSObject
@end

@implementation MyObject

- (void)saveToFile: (NSString *)fileName
{
        NSDictionary * profileList = [NSDictionary dictionaryWithObjectsAndKeys:@"Test string", @"test", nil];
        NSLog(@"%@", [profileList description]); // This Shows Up Fine
        profileList description] writeToFile:@"List.xml" atomically:YES]; // This saves fine
        [[[[NSDictionary dictionaryWithDictionary:profileList] writeToFile:fileName atomically:YES]; // This just doesn't work!
}
@end


int main()
{
        MyObject * myObject = [[MyObject alloc] init];
        [myObject saveToFile:@"list2.xml"];
}




BR,
Cristi

----
Please see HowToAskQuestions ... "doesn't work" is rather useless. What exactly happens. What error(s) are you receiving?


----
I'm having the same problem. Using Cristi's test function, profileList description] writeToFile:atomically:] succeeds but [[[NSDictionary writeToFile:atomically:] returns false and does not create the file. There is no permissions problem (both the working and non-working calls were writing to the same directory (/tmp), neither file existed beforehand, and there is ample free space on disk). dtruss shows this:

    
lstat64("myfilename\0", 0x7FFF5FBFC5B0, 0x7FFF70534630)		 = -1 Err#2

 
and nothing more. I tried using a .xml suffix for the filename, and that didn't help either. errno is "Operation timed out", which I suspect is irrelevant. This is on OS 10.6.4.

