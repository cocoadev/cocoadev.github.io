---
layout: page
title: NSTextviewAttachmentDragging
---

Adding an image to a textview programatically:

This code is all you need. Simply make sure that the textview has "Graphics Allowed" checked.
    
NSOpenPanel *open = [NSOpenPanel openPanel];
[open setAllowsMultipleSelection:NO];
[open setCanChooseDirectories:NO];
if ([open runModalForTypes:[NSArray arrayWithObjects:@"jpg",@"jpeg",@"gif",@"png",nil]] ==  NSOKButton)
{
  NSString *thePath = open filenames] objectAtIndex:0];

  [[NSFileWrapper *filew = [[[NSFileWrapper alloc] initRegularFileWithContents:[NSData dataWithContentsOfFile:thePath]] autorelease];
		
  [filew setFilename:thePath];
  [filew setPreferredFilename:[thePath lastPathComponent]];

  NSTextAttachment *fileAttachment = [[[NSTextAttachment alloc] initWithFileWrapper:filew] autorelease];
  NSImage *image = [[[NSImage alloc] initWithContentsOfFile:thePath] autorelease]; 
  NSTextAttachmentCell *attachmentCell = [[[NSTextAttachmentCell alloc] initImageCell: image] autorelease];
  [fileAttachment setAttachmentCell:attachmentCell];
  NSAttributedString *fileAttString = [NSAttributedString attributedStringWithAttachment:fileAttachment];

  [textview insertText:fileAttString];

}


----

Hi, I am trying this code. The attachment can be dragged to TextEdit or Mail.app but when dragged to Desktop no (+) sign is shown on mouse pointer and when dropped it creates an empty Untitled clipping. Anything else should be added to make Finder accept this as file? --AndreyBabak

