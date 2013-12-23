---
layout: page
title: SubclassingNSData
---

I am trying to subclass General/NSData or General/NSMutableData.

#import <Cocoa/Cocoa.h>

@interface General/MyData : General/NSData {

	int aValue;
}
@end

Everything compiles fine but when I allocate and initialize using

	myData = General/[[MyData alloc] initWithContentsOfFile: myFilepath];

I get the following message

*** initialization method -initWithBytes:length:copy:freeWhenDone:bytesAreVM: cannot be sent to an abstract object of class General/MyData: Create a concrete instance!
 
It seems that the subclass is no a real instance. I can't find any info subclassing General/NSData on the Apple site or any info on which methods must be overridden!

Any Ideas? 

---- 

General/NSData is most likely a Class Cluster.  Check out General/ClassClusters on what they are and how they work.


Michael Gyngell
