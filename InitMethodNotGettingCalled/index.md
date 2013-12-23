---
layout: page
title: InitMethodNotGettingCalled
---

I have a document based application that uses a subclass of an NSTextView.  Does anyone have an idea as to why my initWithFrame: method isn't getting called?

----

Try seeing if initWithCoder works. When you change the class of a prefab NSTextView (dragged from the "Cocoa-Text" palette) to a custom NSTextView and if this custom NSTextView's interface was parsed by IB, IB will archive this view a little different than a generic "CustomView".

    
- (id)initWithCoder:(NSCoder *)decoder {
    self = [super initWithCoder:decoder];
    if (self) {
        
        // do your custom view setup here

    }

    return self;
}
 

----

According to the FAQ in Interface Builder (Help -> FAQ), cocoa section:

**Why isn't my initWithFrame: method called?**

initWithFrame: is only called when you have added a Custom View object to your window. The initWithFrame: message is sent to the class you specified in the Attributes Info Panel. Other widgets will not receive initWithFrame: messages. In this case you should just implement a -(void)awakeFromNib method to handle any further setup at runtime.

