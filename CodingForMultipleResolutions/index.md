---
layout: page
title: CodingForMultipleResolutions
---

I have written a Cocoa adaptation of a board game and have been running it and testing it on a 1280x1024 resolution screen where most of the screen is used up by the various windows of the game.  My question is what coding techniques should be used so that the application will look as good and scalable down to 1024x768 and 800x600.  I can scale my custom view fairly easily, but I am unsure about the various windows in the NIB file that have fonts assigned by dragging the resource objects onto them.

----

if you code your objects and drawing to deal gracefully with arbitrary sizes (such as using a larger or smaller image on your playing cards), and lay out your objects based on the bounding rectangle of the window, you'll get a lot of that for free.  The user sizes the window smaller and once things cross a certain threshold, they use the smaller images, for instance.  For things like fonts, you should run it and see if the fonts set for 1280x1024 look ok in 800x600.  They might.  You also don't want to go too far in customizing the smaller resolutions.  Sometimes it's someone with poor eyesight that has things cranked down like that.  If you make your fonts proportionally smaller, you might make it unusable for that user.

----

How do you lay out objects in interface builder based on the bounding rectangle of the window?  I have added some code in "awakeFromNib" in my main application to resize and reposition the windows proportionally for the screen size like this:

    
- (void)awakeFromNib
{
	NSRect  screenRect;
	NSRect  windowRect;

	screenRect = [[NSScreen mainScreen] frame];
	windowRect.origin.x = 0;
	windowRect.origin.y = (screenRect.size.height * 0.428);
	windowRect.size.width = (screenRect.size.width * 0.3);
	windowRect.size.height = (screenRect.size.height * 0.55);
	[playerWindow setFrame:windowRect display:YES];
                     ...
// Four other windows reset the same way follow


----

Check out the Sproing example at Apple's site. It's great when you are trying to figure out how springs work because you can get instant feedback when you're playing around with it:

http://developer.apple.com/samplecode/Sproing/Sproing.html

Here's the basics: the outer springs determine the position of the view in the window. The inner springs determine the size. If the inner springs are set, the view will resize with the window, if not, it will stay the same size. If a line is set on one of the outer springs, the view will lock itself to that edge of the window. So, if you want the view to lock itself to the top right of the window, set a line to the top and right, and set a spring to the bottom and left. Hope that helps.

-- RyanBates

----

Okay, I understand that.  But what about the system fonts in the dialog.  As the size of the object on the window gets smaller, eventually the text disappears because the font is too big for the dimensions.  If you shift down the screen resolutions, the window sizes need to shrink to match, but the font sizes all stay the same, and there isn't room on the window for it anymore.  So do I have to change each object's font individually once I test and find that the resolution size is not optimal for my application?  (Or conversely, start with a small font size for 800x600 and increase it if I want it to still be readable on 1600x1200 resolutions.)

----

Generally, I would say changing the font size depending upon the user's screen resolution is a bad choice. However, if this is a game, it might be acceptable. A font at 10-12 points should be plenty readable on the higher resolutions - but it ultimately depends on the size and quality of the user's monitor. If the text doesn't fit on a small window at 11 points, then you may want to re-think the UI design or shorten the text. -- RyanBates

----

I did some more research and came up with a solution to my problem that looks like it will work quite well.  I made 3 copies of the MainMenu.nib file, and loaded them into my project.  I will alter them as necessary to look good enough in the required resolutions.  I removed the above code in awakeFromNib since it won't be necessary anymore and altered main.m to look like this:

    
int main(int argc, const char *argv[])
{
	NSRect  screenRect;

	screenRect = [[NSScreen mainScreen] frame];

	NSApp = [NSApplication sharedApplication];

	if (screenRect.size.width < 1000)
		[NSBundle loadNibNamed:@"Main800" owner:NSApp];
	else if (screenRect.size.width < 1200)
		[NSBundle loadNibNamed:@"Main1024" owner:NSApp];
	else if (screenRect.size.width < 1600)
		[NSBundle loadNibNamed:@"MainMenu" owner:NSApp];
	else
		[NSBundle loadNibNamed:@"Main1600" owner:NSApp];

	[NSApp run];
	return 0;
//  return NSApplicationMain(argc, argv);
}


I don't know if this is proper design, but I think it will suit my purposes.  Thank you for your help though.

