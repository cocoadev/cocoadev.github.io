---
layout: page
title: EmbedImageInNSAttributedString
---

from the cocoabuilder list - something many of us need on a regular basis - thanks Uli!

*cocoabuilder is an archive of Apple's CocoaDevMailingList, not an entity unto itself.*

    FROM : TeachText
DATE : Mon Mar 28 16:58:05 2005

Here's how to append an image to an NSMutableAttributedString:

- (void)appendImage:(NSImage*)img
{
  NSFileWrapper *fwrap = [[[NSFileWrapper alloc] initRegularFileWithContents:[img TIFFRepresentation]] autorelease];
  NSString *imgName = [([img name] ? [img name] : @"image") stringByAppendingPathExtension:@"tiff"];

  [fwrap setFilename:imgName];
  [fwrap setPreferredFilename:imgName];
  NSTextAttachment *ta = [[[NSTextAttachment alloc] initWithFileWrapper:fwrap] autorelease];
  [self appendAttributedString:[NSAttributedString attributedStringWithAttachment:ta]];
}

