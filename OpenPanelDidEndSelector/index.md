---
layout: page
title: OpenPanelDidEndSelector
---



OK I'm stumped.  I really try to go through the documentation before I post dumb questions on here and I just can't seem to work this one out. Please bear with me if its a dumb mistake.

I'm just going to an example in the Coca Programming for OS X (1st ed.) book. I'm attempting to have an A<nowiki/>ppController object present an open panel as a sheet and let me choose an image.  So I have the following code:

    
- (IBAction)open:(id)sender
{
    NSOpenPanel *openPanel = [NSOpenPanel openPanel];
    [openPanel beginSheetForDirectory:nil
				 file:nil
				types:[NSImage imageFileTypes]
		       modalForWindow:[stretchView window]
			modalDelegate:self
		       didEndSelector:@selector(openPanelDidEnd:returnCode:contextInfo:)
			  contextInfo:nil];
}

- (void) openPanelDidEnd:(NSOpenPanel *)openPanel
	      returnCode:(int)returnCode
	     cintextInfo:(void *)x
{
    NSString *path;
    NSImage *image;
    if(returnCode == NSOKButton)
    {
	path = [openPanel filename];
	image = [[NSImage alloc] initWithContentsOfFile:path];
	[stretchView setImage:image];
	[image release];
    }
}


Now the panel is presented and allows me to select am image just fine, but nothing happens after that.  I've placed break points on almost every line in those two methods, and in the [s<nowiki/>tretchView s<nowiki/>etImage:image] method.  The breakpoints in the open method show me that it is being called (obviously if the sheet is displayed) but no other break points are hit.

Is something wrong with my <code>didEndSelector:@selector(openPanelDidEnd:returnCode:contextInfo:)</code> line?

Like I said, I've checked the documentation & it just doesn't point me to what the problem is.  Can any of you guys maybe help clear it up?

Thanks a lot
CliffPruitt

----

You are aware that the delegate method misspells "contextInfo", aren't you?  Is it misspelled in the source or was that just a typo here?

----

Can we maybe just pretend that I never posted this question...?

Ugh...  yes it was misspelled in the source.  I thought there might be a misspelling somewhere & looked at it 20 times but just couldn't see it.

Thanks for helping me catch it.  Sorry for wasting screen space on something so dumb. :-)

*I only have 1228800 pixels and you wasted 1179140 of them! I can never use those pixels again! Thanks alot man! :p*

----

It happens, although luckily not often where other people see it. :)

