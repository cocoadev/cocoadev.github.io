---
layout: page
title: ImageInUrlString
---

Describe ImageInUrlString here.


In my roll-your-own Cocoa browser, I am trying to get the webView:didReceiveIcon:forFrame: method for the WebFrameLoadDelegate delegate to work.   Here is the code I am using which is showing the 'abc' string in the url address bar but no image attachment.  Am I mistaken in thinking that a favicon can be included in a text field in this manner or I am missing something here?

    
- (void)webView:(WebView *)sender didReceiveIcon:(NSImage *)image forFrame:(WebFrame *)frame {
	NSString *urlValue = @"abc ";
	if (frame == [sender mainFrame]) {
		NSTextAttachment *attachment = [[[NSTextAttachment alloc] init] autorelease];
		if ([(NSCell *)[attachment attachmentCell] respondsToSelector:@selector(setImage:)]) {
			NSLog(@"icon : %@",image);
			[(NSCell *)[attachment attachmentCell] setImage:image];
			NSDictionary *theAttributes = [NSDictionary dictionaryWithObjectsAndKeys: 
				attachment, NSAttachmentAttributeName,
				NULL];
			NSAttributedString *attrname = [[[NSAttributedString alloc] initWithString: urlValue attributes:theAttributes] autorelease];
			NSMutableAttributedString *imageString = (id)[NSMutableAttributedString attributedStringWithAttachment: attachment];
			[imageString appendAttributedString: attrname];
			NSLog(@" stringer: %@",imageString);
                     //sends imageString to browser
			[urlString setStringValue:imageString];
		}
	}
}

Here is the output from NSLog when I go to a page like apple.com.  Here the log shows the string 'abc' (which is showing up in the url address bar) but not the apple favicon that apparently accompanies it.


2007-02-01 13:25:05.677 Z Test[10997] urlString: http://apple.com

2007-02-01 13:25:06.405 Z Test[10997] icon : NSImage 0x373340 Size={16, 16} Reps=(
    NSBitmapImageRep 0x353b10 Size={16, 16} ColorSpace=NSCalibratedWhiteColorSpace BPS=8 BPP=16 Pixels=16x16 Alpha=YES Planar=NO Format=2
)

2007-02-01 13:25:06.428 Z Test[10997]  stringer: abc {NSAttachment = <NSTextAttachment: 0x3e9830>; }


If anyone could give me a pointer on this, it would be most appreciated.

Thanks, Vince

