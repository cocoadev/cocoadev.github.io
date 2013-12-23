---
layout: page
title: ConvertNSImageToNSData
---

I'm using CoreData, and I have an NSTableView with an NSImageCell column. What I want to do is programmatically set a default image to be displayed until it is changed by the end user. I have subclassed my controller and set it up to where it sets a value right after creation of a new object using KeyValueCoding. What I need to know is how to change an NSImage to NSData so that the NSImageCell can understand it when the program runs. Can anyone help me? --LoganCollins

---- 

NSImage has an initWithData: method.  Use it.

    
-(NSImage *)image
{
    return [[NSImage alloc] initWithData:[self imageData]];
}


2005-05-22

*The above doesn't adhere to common memory management principals, you should add an autorelease to it.*

----

That doesn't seem to work either. I've tried every combination with that method that I can think of. With just an image being sent by KeyValueCoding it returns in the log that it recieved NSImage and needs NSData. So I need a way to convert the NSImage to an actual NSData object. Is that what that code does and I just don't see it right? --LoganCollins


----

See NSImageToJPEG or use the NSImage instance method TIFFRepresentation. --zootbobbalu

----

How can that help? I want it to be converted to an NSData object, not a JPEG file. --LoganCollins

----

I think you might me a little mixed up on what an object is and what a flat representation of an object is. Since I'm not looking at your code I have to guess on this, but I think the reason you are being asked for an NSData representation of your image is so that CoreData can archive the image for you. My post is helping you with two ways to provide CoreData with a flat representation (an NSData instance) of your image. When you need to unarchive an NSData object back into an NSImage object use the NSImage instance method     initWithData: method mentioned above. --zootbobbalu

----

Oh, I see what you mean now. Thanks, the TIFFRepresentation method works perfectly. Sorry but I'm going a little brain-dead from being up all night coding. :) --LoganCollins

