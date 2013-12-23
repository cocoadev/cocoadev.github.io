---
layout: page
title: AnatomyOfADotHFile
---

Here is the layout for a header (interface) file.  For this example, we will assume that the name of your class is Foo.

    
//First put in any import statements

#import <Cocoa/Cocoa.h>

// Then forward-declare any non-Cocoa classes
// (Except those being inherited from)
// (See AdviceOnImportingHeaders)

@class BossMan;

// Next, put in this line which declares the name of the class
// and gives the name of the superclass it derives from.  In this
// case the name of the class is Foo and the name of the
// superclass is NSObject.

@interface Foo: NSObject

// Next, in braces, declare your instance variables and your outlets.

{

   NSMutableArray   *foo;
   NSString         *personName;
   float            expectedRaise;
   BossMan*         bossApprovingRaise;

   IBOutlet id          textField;
   IBOutlet id          customView;
   IBOutlet NSTextField *staticText;
}

// Next, declare your actions...

- (IBAction) createEmployee: (id) sender;
- (IBAction) drawCoolStuff: (id) sender;

// and your instance methods.

- (id) init;
- (void) dealloc;
- (void) createFoo;

// Last, end your header with the following line...

@end


Back to HowToProgramInOSX

For how to use forward declarations in a header file see AdviceOnImportingHeaders
(you should not need to import header files directly into your header file except for importing
the requisite super-class header). In the case of category headers, you need to
import the header for the class on which you are adding the category.

forward-declaring your class names via the     @class declaration is far-preferable when you
need a reference to them in a header file.

