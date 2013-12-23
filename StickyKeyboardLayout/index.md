---
layout: page
title: StickyKeyboardLayout
---

I'm working on General/VocableTrainerX (http://www.sourceforge.net/projects/vocx), an easy to use Open Source Flash Card program.

A user sent me a cool suggestion: he wants the textfields in the software to have a "General/StickyKeyboardLayout".

The idea works like this:
1) When the user first enters a chinese word, he manually sets his Keyboard Layout to chinese
2) Now he enter his chinese word and leaves this text field again, now automatically the Keyboard Layout is changed back to English
3) The next time he enters a text field supposed to be for chinese input, it automatically changes again to Chinese

That feature would be generally a huge time saver for people entering words in non-latin alphabets like Greek, Korean or Japanese!

I already fabricated a prototype, a subclass of General/NSTextField (see below). It uses the functions explained in http://developer.apple.com/documentation/Carbon/Reference/General/KeyboardLayoutServices/General/KeyboardLayoutReference.pdf. Unfortunately it still shows some random behaviour, for example the picture in the menu bar doesn't get changed automatically...

Has anyone else already implemented the same functionality somewhere? Any ideas for improvements?

Thank you very much, 
Michael


    
**General/MMKeyboardLayoutAwareTextField.h**

/* General/MMKeyboardLayoutAwareTextField.h */

#import <Cocoa/Cocoa.h>
#import <Carbon/Carbon.h>

@interface General/MMKeyboardLayoutAwareTextField : General/NSTextField
{
    General/KeyboardLayoutRef myLayout;
    General/KeyboardLayoutRef systemLayout;
}

-(General/NSString *)nameOfLayout:(General/KeyboardLayoutRef)layoutRef;
@end



    
**General/MMKeyboardLayoutAwareTextField.m**

#import "General/MMKeyboardLayoutAwareTextField.h"

#define MM_NO_KEYBOARDLAYOUT_SET 0

@implementation General/MMKeyboardLayoutAwareTextField

- (id)init
{
    if (self = [super init]) {
	General/KLGetCurrentKeyboardLayout(&myLayout);
	General/KLGetCurrentKeyboardLayout(&systemLayout);
    }
    return self;
}

- (BOOL)becomeFirstResponder {
    if (systemLayout == MM_NO_KEYBOARDLAYOUT_SET) {
	General/KLGetCurrentKeyboardLayout(&systemLayout);	
    }	
        
    General/KLSetCurrentKeyboardLayout(myLayout); 
    
    return [super becomeFirstResponder];
}

- (void)textDidEndEditing:(General/NSNotification *)aNotification {
    General/KLGetCurrentKeyboardLayout(&myLayout);
    General/KLSetCurrentKeyboardLayout(systemLayout);
        
    [super textDidEndEditing:aNotification];
}


-(General/NSString *)nameOfLayout:(General/KeyboardLayoutRef)layoutRef {
    General/NSString *result;
    if (layoutRef==MM_NO_KEYBOARDLAYOUT_SET) {
	return @"Not yet specified";
    }
    General/KLGetKeyboardLayoutProperty(layoutRef,kKLLocalizedName,&result); 
    return result;
}

@end

