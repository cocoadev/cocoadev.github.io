---
layout: page
title: IBSizeToFit
---

If you want your custom view Palette to support "Size To Fit" from the Layout menu in Interface Builder add a category on your custom view in your IB Palette code that implements

    
- (void)ibSizeToFit


Your implementation of ibSizeToFit should adjust the view's frame to what you want it to be.

This applies to IB from 10.4 and previously.

DaveMacLachlan

----

Did you try implementing -[NSView sizeToFit]?  That's a public method that should be enough.

