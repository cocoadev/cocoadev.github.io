---
layout: page
title: EasierKVCThroughPreprocessorMacros
---

The macros below make it faster to write KVC accessors. They are used as follows:

    
// in SomeClass.h
@interface SomeClass : NSObject {
    NSString* val_title;
}

L0_KEY(NSString*, title, setTitle)

// ...

@end

/**********************/

// in SomeClass.m
@implementation SomeClass

L0_KEY_IMPL(val_title, NSString*, title, setTitle)

// ...

@end



----

L0Macros.h:
    

#ifndef L0_MACROS_H
#define L0_MACROS_H

#define L0_KEY_IMPL(name, type, getter, setter) \\

	- (type) getter { \\

		return name; \\

	} \\

	\\

	- (void) setter : (type) value { \\
	
		if (name != value) { \\
		
			[name release]; \\
			
			name = [value copy]; \\
			
		} \\
		
	}

#define L0_KEY_NONOBJECT_IMPL(name, type, getter, setter) \\

	- (type) getter { \\
	
		return name; \\
		
	} \\
	
	\\
	
	- (void) setter : (type) value { \\
	
		name = value; \\
		
	}
	
#define L0_KEY(type, getter, setter) \\

	- (type) getter; \\
	
	- (void) setter : (type) value;

#endif


