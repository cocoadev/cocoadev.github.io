---
layout: page
title: NSBrowserRedraw
---

I update the contents of my NSBrowser via the normal NSBrowser delegate method: 

- (void)browser:(NSBrowser *)sender willDisplayCell:(id)cell atRow:(int)row column:(int)column

I am giving the 'cell' provided from that method a nice attributed string that displays just great.

However, when the user selects my cell in the browser, I want to detect that selection of an item and change the color (attributed string) of that cell to something else.

I can't figure out a neat way to prompt the browser to refresh, first off - I am trying [myBrowser reloadColumn:0] - (where '0' is the column I am working with), and I am calling this every time the browser is clicked (via its main target/action method).

This does work in that it allows me to refresh a column, and the browser: willDisplayCell: ... method is called to refresh the cells in that column.

I put a [myBrowser selectedRowInColumn:0] in the willDisplayCell: method and it goes crazy when it updates. So there seems to be a problem getting the currently selected cell during a column update.

How do I detect when a cell has been clicked and change its attributed string in an NSBrowser.

----

How about making the method above conditional on which cell is selected? That is, if the row/column it gives you is the current selected cell, set "special" attributes, otherwise set normal ones.

----

Tried it - by doing (in the willDisplayCell: ... method):

if (cell == [browser selectedCellInColumn:0]) ... make changes to 'cell's attributed string. But this doesn't work because it always returns false. Tried using isEqual: as well.

The reason WHY I think it always returns false, even though something is selected is because I am using [browser reloadColumn:0] to 'refresh' the browser, and I have a feeling that checking for the selectedCell while the browser is doing a full column reload causes problems with returning the selectedCell properly. 

I use NSLog(@"Currently selected row is %d", [browser selectedRowInColumn:0]) in the willDisplayCell... method and it gives me strange results indeed when I prompt a refresh via [browser reloadColumn:0] - it will never give me the correct index of the selected row - just oscillating between -1 and 1 as i click around. weird.

----

What if you based your conditional off of     [cell isHighlighted]?

----

This doesn't seem to do the trick either. Here's how I have it set up:

    
- (void)browser:(NSBrowser *)sender willDisplayCell:(id)cell atRow:(int)row column:(int)column
{
/* Alter the appearance of this cell to match what should be at this column and row */
	
	if (column == 0)
		{
		/* Users */
		if ([cell isHighlighted])
			[cell setAttributedStringValue:[self browserUserNameAttributedStringFromString:[discoveredServicesDictionaries objectAtIndex:row] objectForKey:@"keyNetServiceObject"] name]isSelected:YES;
		else
			[cell setAttributedStringValue:[self browserUserNameAttributedStringFromString:[discoveredServicesDictionaries objectAtIndex:row] objectForKey:@"keyNetServiceObject"] name]isSelected:NO;
		NSImage* iconImage = discoveredServicesDictionaries objectAtIndex:row] objectForKey:@"keyUserImage"];
		[cell setImage:iconImage];
		}
}

...
...
...

- ([[IBAction)browserClick:(id)sender
{
	[browser reloadColumn:0];
}



Plus, with NSBrowser there doesn't seem to be any nice equivalent to NSTableView's delegate method     selectionDidChange: (I think that's what it's called but that's off the top of my head. That would certainly be something I'd be interested in having in this situation, but it's not there.

----

NSBrowser does have     - (BOOL)browser:(NSBrowser *)sender selectRow:(int)row inColumn:(int)column; Its purpose seems to be to allow you to implement custom selection behavior, which is something that might work for you. If you simply call     setHighlighted: on the right cell then you ought to be able to duplicate the bulit-in behavior. Otherwise, you might be able to subclass NSBrowser and override     selectRow:inColumn: to send a notification like the one you want.

----

Thanks for your input! I did try implementing that method, but it never seems to get called by my browser no matter how much I click around.

From the documentation I know there are 2 ways to be a datasource for NSBrowser, active and passive, I was hoping to use the passive method as it's just simpler, and more like how NSTableView works. But maybe I have to manually create the cells for the browser myself and use the active way of implementing NSBrowser.

Still... there's only so much time one can spend on a little UI nicety like this without being tempted to move on(!).

It does strike me that NSBrowser is the ugly forgotten half-brother of NSTableView, who seems to have gotten just a sliver of the attention (from both the AppKit team, and from developers) that NSTableView has. (Maybe understandably because of the ubiquity/popularity of NSTableView).

----

I have done Extensive work over the course of the last year with Browsers.  The browser class is messy, out of date, and is a big pain, but Browsers definitely call -(IBAction)selectCell:(id)sender
I have just checked it in my own code (using the debugger), and I use that as a hook to do some serious work to get my browser to be compliant with Bindings.
I believe I use a Passive delegate but its been so long since I started my browser mini-project that I'm not sure.
I'm a hobbyist, and my browser is learning to respond to drag n drop, keyboard control, drag n drop reordering... and as mentioned before... bindings.  the only thing missing is keyboard control (well its already partially there, but i need to hook up the delete key).  I sometimes don't get to program for months at a time.

What I had to do to get this all to work was seriously messy: categories, browser subclasses, a whole new understanding (for me) on the nature of delegates, many many sad nights of trial and error, and the unhappy realization that nothing works as good as a browser does in my application design.  but I've gotten something that works almost exactly like I envisioned.  now the only thing I need to get working is that when I hit the delete key... my browser tells my application to delete the selection.

While not usually the case with browsers, in this case, there is an easy way to get it to work, and if you find yourself banging your head against the wall, then you're probably over-analyzing the problem. It may just be that your delegate isn't overriding the correct "selectCell" method. either you have the wrong method, or you have it in your code slightly different?

----

Detecting a click is simple: create an action
    
-(IBAction)doBrowserClick:(id)sender
{
    NSLog(@"received click for %@",sender);
}


then set the target for the NSBrowser to the method defined above in IB; it'll be called on every click.

----

I was going to use an NSBrowser for my recent project, but found that it was just too difficult. If you have a finite number of columns, you could try replacing the browser by a series of NSMatrix<nowiki/>es or one-column NSTableView<nowiki/>s. BTW, the browser delegate's     select... methods are only called for programmatic selection, rendering them practically useless. I would think others would agree with me when I say that NSBrowser needs a full overhaul from Apple, although NSTreeController support is nice (if it works, I haven't tried it yet). --JediKnil

----

It works, but you can't use the willDisplayCell: delegate method.  You get a runtime gripe about having to implement numberOfRowsInColumn: or creatRowsForColumn:, which for a bindings-fed NSBrowser is kind of bizarre.

----

dont use : NSCell's isHighlighted method.  the browser itself keeps track of whats selected, and you may inadvertedly miss the selection, or get the selected cell of a collumn to the left of the one you want to be working in.

use NSBrowser's  selectedCell  method instead. it always gives you the rightmost, bottommost selected cell.

in addition, reloadCollumZero... I think it makes the browser forget the current selection. in short, don't use that either.  try to identify the correct cell and then tell IT to redraw. this involves figuring out where it lives in its superview, setting its display attributes, and then telling it to redraw itself.  it avoids any nasty Loss of selection.

and one more little tidbit: 
you can override:
- (BOOL)browser:(NSBrowser *)sender selectRow:(int)row inColumn:(int)column
in your delegate and from that point on, you are responsible for making selections. I think this is what you want because i "think" that willDisplayCell, isn't called when you click on a cell.  I could be wrong, but I Know willDisplayCell is not always called when you think it should be.

----

I'm trying to use an NSBrowser to do some finder-like things in my application, namely, navigating through the directory hierarchy. Each time I click on a non-leaf element the NSBrowser's delegate sets the titles and icons for all the cells in that column. I'm using an NSWorkspace to get the icons for each cell.

The problem is that when scrolling in the column that isn't the right-most column, the icons aren't updated properly. For example, if I select the Applications folder, I'll have a column that needs to be scrolled.  If the applications folder is the right most column, and I scroll it, the icons will be the correct system icons.  If I were to select the Utilities folder, the applications folder will no longer be the right most column.  If I scroll the applications column, the cells that get displayed have the generic file or folder icon. What's the problem here?

----

Are you saying that your browser does not properly update the icons of any column besides the last one,
or are you saying that the contents of your last column don't stay current with the selected branch node?
 
My guess is that you have a memory problem, however you get the icon, it is not being retained, and the icon becomes generic, because the NSImage is lost right after you use it. Of course this could be wrong, I don't see your code, and haven't worked with NSWorkspace myself, but it sounds like a memory thing.

----

A possible direction to pursue for this (undiagnosed) problem may be to use code such as is found in NSBrowserIcons

