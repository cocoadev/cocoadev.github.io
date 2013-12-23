---
layout: page
title: AnatomyOfADotMFile
---

Here is the layout for the implementation file.  In this case, it is an implemenation file for the class Foo.

    
//First, import the header file for your class

#import "Foo.h"

// Next, put in this line which declares the name of the class.
//  In this case, the name of the class is Foo.

@implementation Foo

// Next, write the code for all of your instance methods

- (id) init
{
   self = [super init];

   if (self)
   {
      [self createFoo];
   }

   return self;
}

- (void) createFoo
{
   foo = [[NSMutableArray alloc] init];
}

- (void)dealloc
{
   [foo release];
}

// Last, end your implementation file with the following line

@end


Back to HowToProgramInOSX

