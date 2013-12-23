---
layout: page
title: NSTextAttachment
---

NSTextAttachment objects are used by the NSAttributedString class cluster as the values for attachment attributes (stored in the attributed string under the key named NSAttachmentAttributeName). The objects you create with this class are referred to as text attachment objects, or when no confusion will result, as text attachments or merely attachments. See the NSAttributedString and NSTextView class specifications for general information on text attachments.

A text attachment object contains an NSFileWrapper, which in turn holds the contents of the attached file. It also uses a cell object conforming to the NSTextAttachmentCell protocol to draw and handle mouse events. Most of the behavior of a text attachment is relegated to the file wrapper and the attachment cell. See the corresponding class and protocol specifications for more information.

*See Also:* NSTextAttachmentCell

**Related Apple Documentation Links**

*NSTextAttachment - http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSTextAttachment.html
*Protocol - http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Protocols/NSTextAttachmentCell.html
*Text Attachments - http://developer.apple.com/documentation/Cocoa/Conceptual/TextAttachments/index.html
*Text System Architecture - http://developer.apple.com/documentation/Cocoa/Conceptual/TextArchitecture/index.html


----
**How Can I add an attachment (image or file) to an NSTextView (its attributed string)?**

You must create an NSTextAttachment and insert it into an attributed string (see NSAttributedString and NSMutableAttributedString). See the code below (from ButtonsInTableOrOutlineViews).

    
// Create a file wrapper with an image.
NSString * imgName = @"my_image.tif";
NSFileWrapper *fwrap = [[[NSFileWrapper alloc] initRegularFileWithContents:
        [[NSImage imageNamed:@"my_image"] TIFFRepresentation]] autorelease];
[fwrap setFilename:imgName];
[fwrap setPreferredFilename:imgName];

// Create an attachment with the file wrapper
NSTextAttachment * ta = [[[NSTextAttachment alloc] initWithFileWrapper:fwrap] autorelease];

// Append the attachment to the end of the attributed string
// (assumes "attrStr" already exists).
[attrStr appendAttributedString:[NSAttributedString attributedStringWithAttachment:ta]];



The above example's method of inserting the attachment into an attributed string is a 'shortcut' but an attachment can be inserted anywhere in an attributed string (if someone would be so kind as to flesh out the example to demonstrate this).

Note that sending YES to the NSTextView's -setImportsGraphics: method will allow a user to drag any image or file to the NSTextView, automatically creating the attachment for you.

----

I'm trying to wrap text around NSTextAttachment cells. I've had success with creating a "hole" in a textview by subclassing NSTextContainer. I was able to create rectangular hole on the left side of the text field that the text flowed around. An image could be drawn here. However, this would probably require custom drawing for each image and this each image would not be able to function as a regular NSTextAttachment. 

I would like for the NSTextView to rearrange the text of a textview on the fly based on the NSTextAttachment present. Ideally, the text before the image would be layed out as normal, then the image would be transferred to the next line. If the line before the image is not complete, the text after the image would complete that line and then come down beside the image (to the right for a left aligned image) in regular line heights. Once the space beside the image was taken up, the text would continue flowing as normal. As far as selection goes, the image would be selected after the text immedietly preceding it if there was no custom NSTextContainer.

Should I be looking into doing something different than NSTextAttachment?

----

**How can I add custom controls (like a button) to my NSTextView so that they flow with the text as it's edited?**

This is possible since an NSTextAttachment uses an NSTextAttachmentCell (a subclass of NSCell). See ControlsMixedWithTextHowTo for more information.

----

Actually, -setImportsGraphics: allows ANY file to be dragged in, whether it's a graphic or not. Either way, it gets embedded into the NSTextView.

----



**Has anyone create an image attachment that allows the user to resize the image and allow the text to flow around it?**
"how to write the text ON the image?"

----

I don't think you'd do this at the NSTextAttachment level. I forget the document that explains this, but you're surely going to have to work with NSLayoutManager.

----
Could someone please explain how I might extract any image attachments that might be in an NSAttributedString?

