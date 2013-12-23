---
layout: page
title: NSTextFieldAutoselect
---



Hello,

I need a simple information for a simple need. I want to know how I can select part of the text contained in a NSTextField. There's already [aField selectText:self], but that one selects everything in the aField.

Another thing. I would like to know why the [colorWithDeviceRed:r green:g blue:b alpha:a] method uses decimal numbers as parameters for RGB or CMYK, or whatever. Is there anyway to do it so it uses a 0-255 value instead of a float between 0 and 1 ?

Thanks for your advice...

----

Yes, use [colorWithDeviceRed:r/(float)255 green:g/(float)255 blue:b/(float)255 alpha:a/(float)255]

----

It doesn't look like there is a way to set an arbitrary selection in an NSTextField.  I looked through the NSTextFieldCell and NSTextStorage class docs, too, and to no avail.  If you need to select arbitrary ranges, it looks like you'll need to go with a bigger control, like an NSTextView, or build your own.  -- AgtAlpha

----

After three hours of trying to figure out a way to set what is selected in an NSTextField by default I finally figured out a way to do this. 

First you have to subclass an NSWindow object and override the "makeFirstResponder" method: 

    
@interface MyWindow:NSWindow {
    NSTextField *myTextField;
}
-(void)setMyTextField:(id)_myTextField;
@end

@implementation MyWindow

-(void)setMyTextField:(id)_myTextField {
    myTextField=_myTextField;
}

-(void)makeFirstResponder:(id)sender {
    id currentEditor;
    NSRange endOfString;
    [super makeFirstResponder:sender];
    if (sender==myTextField) {
        currentEditor=[myTextField currentEditor];
        if (currentEditor) {
             endOfString=NSMakeRange(myTextField stringValue] length], 0);
             [currentEditor setSeletedRange:endOfString];
        }     
    }
}

@end


Now you have to set the instance variable "myTextField" in a controller object. If you are working with a document based app then a good place to do this is in the "windowControllerDidLoadNib" method.

    

- (void)windowControllerDidLoadNib:([[NSWindowController *) aController {
    id win=[aController window];
    [win setMyTextView:myTextView];
}



if you are working with a non document based app then just set "myTextView" in the awakeFromNib method of the primary controller object (i.e. MyObject). 

Dont forget to drag the custom window object (either MyWindow.m or MyWindow.h file icon) from PB to the nib window in IB and change the setting in IB for the window with the NSTextField to MyWindow instead of NSWindow (do this by selecting the window and then command+5 so you can set the custom class to MyWindow). 

--zootbobbalu

----

Does anyone know how to force an NSTextField to select all of its text when initially clicked (instead of invoking the cursor)? I can get a button to do a [textView selectText: nil] but can't seem to be able to force the TextView to recognize this when it invokes the action it's connected to. Any thoughts?

