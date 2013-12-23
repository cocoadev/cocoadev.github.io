---
layout: page
title: ProblemWithrespondsToSelector
---



I have a subclass of NSImageView called GelImageView. The class has a number of standard methods to be activated from the menu bar, and is part of a large project. Amongst others, it has the following methods:
    
- (IBAction)cut:(id)sender;
- (IBAction)paste:(id)sender;
- (IBAction)copy:(id)sender;
- (IBAction)selectAll:(id)sender;
 
These methods are all hooked up to FirstResponder in IB.
Out of a large number of methods it handles properly, I found that the cut, copy and paste menu items in the Edit menu were not being enabled when GelImageView became first responder, despite these methods being implemented by GelImageView. Further investigation showed that they were not enabled because respondsToSelector was returning NO for these methods. But these methods are definitely in the class! I overrode respondsToSelector to return YES for these methods, and everything works fine; this demonstrates to me that there are no problems with the cut, copy and paste methods themselves. selectAll, which looks identical to cut, copy and paste, works fine.

So what stupid thing could I have possibly done to make respondsToSelector return NO for methods the class clearly has?

Julian Blow

----

Don't override respondsToSelector:.

You probably inherited a validateMenuItem: method from NSImageView, and it's disabling those menu items because it's not editable, or doesn't have content data, etc. Try overriding validateMenuItem: so that it always returns YES. See the documentation for the NSMenuValidation category for more information.

    
- (BOOL)validateMenuItem:(id <NSMenuItem>)menuItem
{
    return YES;
}


---- Thanks for the suggestion, but it's not a problem in validateMenuItem:, because validateMenuItem never gets called for these methods. respondsToSelector: returns NO, so the menu validation system doesn't even bother checking validateMenuItem: (I've confirmed this by putting an NSLog in validateMenuItem:, and this showed that it's not called for these methods).

----

I created a small project to test this out. Basically, I:


*
1. Made a Cocoa Application project
*
2. Made a subclass of NSImageView called MyImageView
*
3. Added a MyImageView to the window in MainMenu.nib
*
4. Made the image view editable so it would accept first responder
*
5. Compiled and ran
*
6. Clicked on the image view
*
7. Clicked on the Edit menu -- Cut, Copy, and Paste were all enabled (I'm using OS X version 10.4.3)

Here's what MyImageView looks like:

    

@interface MyImageView : NSImageView {

}

@end

@implementation MyImageView

- (void)cut:(id)sender
{
    NSLog(@"cut!");
}

- (void)copy:(id)sender
{
    NSLog(@"copy!");
}

- (void)paste:(id)sender
{
    NSLog(@"paste!");
}

- (BOOL)validateMenuItem:(id <NSMenuItem>)menuItem
{
    return YES;
}

@end



The menu items remained disabled until I added the validateMenuItem: method.

----I agree with your interpretation of the need for calling validateMenuItem for this subclass as you demonstrate, but I'm afraid it's not my problem. I already had a validateMenuItem which looked for appropriate things (whether an item was selected in the view for cut and copy, whether there was something appropriate on the pasteboard for paste, etc). But the problem is that unless I override respondsToSelector, validateMenuItem is not called for the cut, copy and paste menu items, although it is called for several other menu items (including selectAll:) (I checked this with an NSLog that printed the name of all menu items called for validation). The reason validateMenuItem is not called for cut, copy and paste is that respondsToSelector is returning NO for them. That's why I had to override respondsToSelector, and I know it's not the right thing to do! But I just can't work out why respondsToSelector can possibly be returning NO for methods included in the class. 

----

The code I wrote was to duplicate the description of your custom NSImageView subclass. If that doesn't exhibit the problem (and it doesn't) then problem is elsewhere, in some code you *haven't* described. I don't have any additional suggestions without more information. I'd recommend starting with a blank subclass of NSImageView, then adding bits of code from your existing custom subclass. Add it in little bits until you see the problem again. Once you see the problem, you know that it's in the code you just added. Then, fix the problem, and continue until you've added back all your custom code and you've arrived at the complete implementation of your original custom NSImageView subclass. Except now, it's bug free. :)

----

Thanks for the moral support - it's solved! 

Although I'm developing for OS 10.3, I recently upgraded to 10.4. What I now see is that the NSImageView class has been expanded with two new methods: setAllowsCutCopyPaste and allowsCutCopyPaste. There's virtually no documentation on this - all it says is "Specifies whether the receiver allows the user to cut, copy and paste the image contents." If I set setAllowsCutCopyPaste to YES, then I find respondsToSelector returns YES for cut, copy and paste. Personally, I think that's a poor way of implementing functionality. My cut, copy and paste methods don't do anything to the underlying image, but to shapes the user draws over the image. I think Apple should find a proper way of allowing the image to be  cut, copied or pasted that doesn't alter the behaviour of respondsToSelector, which is a basic NSObject method and not to be mucked around with!

*Apple should really implement this using validateMenuItem:. I would highly consider filing a bug.*

Have done so, Problem ID: 4357995.

----
Is it possible that Apple added -setAllowsCutCopyPaste: and -allowsCutCopyPaste to give developers a way to protect their intellectual property ?  For example, a developer may not want their animations, logos, or water marks copied... and by changing -respondsToSelector: there is an extra guard against copying ?  That can't be can it ?  Any developer who can call -respondsToSelector: can get around it returning the "wrong" answers...
----
This really makes no sense. respondsToSelector: is no more effective at guarding against copying than validateMenuItem:.

The only difference between the two as far as menu items go is, when validateMenuItem: returns NO, the menu will be grayed out for certain. But if respondsToSelector: returns NO, it will keep searching up the responder chain for something that does respond to the message. So if you had cut: defined in your window controller or application delegate, it could pick that up instead. Perhaps people were relying on certain behaviors, or Apple came up with some scenario where this would be useful, and decided to allow further searching of the responder chain. That doesn't make a lot of sense, but it's the best explanation I can think of.

----
Actually, things aren't as bad as I originally made out. If I make a new subclass of NSImageView, the problem is not there. It only seems to be there when I use an old NIB file that was made a while ago under OS 10.3 or earlier. Somehow it seems that the new version of NSImageView in the NIB responds properly to the setAllowsCutCopyPaste: command, but the old NSImageView in the NIB does not. I can't work out why this should happen, but they actually look slightly different in IB. Goodness knows what's going on, but I suppose the moral is that if anyone else sees this problem, recreate the NSImageView in IB.

