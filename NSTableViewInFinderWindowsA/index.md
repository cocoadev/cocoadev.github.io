---
layout: page
title: NSTableViewInFinderWindowsA
---



I rewrote some of this to use less lines and is all around more efficiant, I'll submit the project to here just as soon as I remove it from our closed source app :)

----

I put up a website to keep everyone up to date with this project. http://homepage.mac.com/tylers/Development/

TylerStromberg

----

Make all the text and image(s) double-click editable and then you'll get my attention. It's certainly cool so far, but unless the text is editable, you could just draw the whole thing in a big custom view and no-one would know the difference.

----

Um, but you might loose out on all that tableview stuff... :-P Anyways, adding editing shouldn't be hard at all.

----

I've been working on this quite a bit. I tried to add editing to the cells as they are, but it wasn't working for me. So, after many hours of coding, I have converted them to a subclass of NSTextFieldCell instead of just NSCell. They are now passed an attributed string for the name and title (instead of being passed a dictionary of properties). Their drawing is handled by the cell's superclass, as I can't figure out how to get these strings in drawInFrame: I wonder if I can just do a [self attributedStringValue] (or whatever the call is). Any thoughts?

TylerStromberg

----

Table views are cool, but like someone (name, please?) posted above, it would be easier not to use one big table view. In my opinion this is a design flaw: you want to have two functions for a single control. This is also a problem in the real user accounts. It would be a lot easier just to use an NSBox to make the subview and put a table view in it for all the other users. Then just add components for the "static" stuff, like the current user and the login options. This way you don't have to figure out whether or not a special cell was clicked. -- JediKnil

----

Yeah, but then if you have more users than the maximum number that can fit in the visible portion of the table, the scroll bar will only appear in part of the NSBox. IMO, this would look really terrible.

TylerStromberg
----

I was taking a look at what you had posted on your site [http://homepage.mac.com/tylers/Development/]. It would appear that the sources are incomplete.

----

What sources? If you're referring to the Software Update sources, I have not posted them yet. If you're referring to the other stuff, it is not quite completed yet. I am currently occupied working on other stuff, and will not have time to finish it any time soon. If you have any work to contribute, please post it here or email it to me and I will add it to the sources on the website.

TylerStromberg

----

Actually, I was going to contribute, but unfortunately much of what was posted (not the Software Update) does not compile. Specifically PeopleTableViewControl & IQIconTextSubtextCell is missing a bunch of implementation that was declared in the headers.

----

The current code I am working with is no different from that on the site. Try adding a #import for all the interface files to the pch (sorry for not posting the pch). Yes, those two classes are missing a lot of implementation, but that should just throw a warning. The reason all those methods were added is they are in Accounts.prefPane's class-dump.

TylerStromberg

----

When you look at how the Accounts pane actually does it, it uses a "UsersTableViewControl" object, which is a subclass of NSControl, not NSTableView. It does seem like Apple's method here is much more conducive to your purposes than your multiple-function-override method (if that made any sense). It would get especially complicated to implement rows of different heights. I am going to need the multiple-row-height table for one of my upcoming apps, and I find that laying out a bunch of cells yourself is probably a lot easier. --RobinHP

----

Sorry for asking such a silly question, but I cutted and pastedr the code provided, but I've got a problem : when the row is selected, the text remains in black color. How can I make I change to white ?

Julien

----

Like this:

    
-(id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
       NSAttributedString *myString = array objectAtIndex:rowIndex] objectForKey:[aTableColumn identifier; //do whatever you need to to make this into an attributed string
        if ([aTableView selectedRow] == rowIndex)
        {
            //set the string's color to white
        }
        return myString;
}


----

BTW, you can find the Finder hi-light colors under /System/Library/Frameworks/Carbon.framework/Versions/A/Frameworks/HIToolbox.framework/Version/A/Resources

or do a search on the /System folder and search for "purple"
Then you will have access to blue, green, gray, orange, purple, red and yellow

Files are named color_middle.png

Cheers,

Jason Olson

----

Just had a look at the IQIconTextSubtextCell code and it's a bit scaring from a leaking point of view, declaration point of view, etc..

You don't need to declare dealloc, _drawHighlightWithFrame (it's not implemented) and drawInteriorWithFrame in the header.

The dealloc method is lacking the call to [super dealloc];

There's no need to call release on mCellImage since it's autoreleased (obtained via imageNamed:)

Apparently, setObjectValue: is the mother of all sins when it comes to leaking: Basically every allocated instances but mValueDict is missing a release.

The implementation for setLabelCell: is MIA as far as I can see.

And a copy method is missing:

    
- (id) copyWithZone:(NSZone *)zone
{
    IQIconTextSubtextCell * newCell = (IQIconTextSubtextCell *) [super copyWithZone:zone];
    
        
    newCell-> mLabelCell = mLabelCell;
    
    newCell-> mValueDict =[mValueDict copy];

    newCell-> mCellImage =nil;
	                                                                      
    return newCell;
}


A proposal:

    
- (void)setObjectValue:(id )object
{
        NSMutableAttributedString *cellText = [[NSMutableAttributedString alloc] initWithString:[object valueForKey:@"name"]];
        NSMutableAttributedString *cellSubtext = [[NSMutableAttributedString alloc] initWithString:[object valueForKey:@"title"]];
        NSString *imageName = [object valueForKey:@"picture"];

        mLabelCell = object valueForKey:@"labelCell"] boolValue];

        [cellText addAttribute:[[NSFontAttributeName 
                                           value:[NSFont systemFontOfSize:13]
                                           range:NSMakeRange(0, [cellText length])];
        [cellSubtext addAttribute:NSFontAttributeName
                                                value:[NSFont systemFontOfSize:11]
                                                range:NSMakeRange(0, [cellSubtext length])];

        NSArray *keys = [NSArray arrayWithObjects:@"text", @"subtext", @"image", @"labelCell", nil];
        NSArray *values = [NSArray arrayWithObjects:cellText, cellSubtext, imageName, [NSNumber numberWithBool:mLabelCell], nil];
        
        [cellText release];
        [cellSubtext release];
        

        if (mValueDict!=nil)
        {
             [mValueDict release];
        }

        mValueDict = [[NSDictionary alloc] initWithObjects:values forKeys:keys];
}



Stephane

----

After 2 years in Brazil, I'm back and I'll be working on and updating this project soon. I've posted the XCode project on my site, however it's an older one (still looking for the newer version - it's lost somewhere on my HD) so it doesn't include all the bug fixes that were made. Also, Stephane, thanks for the pointers. I realize that there are a lot of declarations in the header file that aren't implemented. They are there because they were in Apple's, and I'll be implementing them as I go. (Others, such as dealloc are there by mistake - oops!) As soon as I find the new project, I'll be streamlining these fixes into it and posting it on the site.

Update: OK, I wasn't able to find the new project, so I used the old one and tried to re-implement everything that I had done (with some success). I was able to fix the different-size rows (as rowHeight now passes a ref to the tableView, so extracting the info is much easier - thanks Apple!). I also cleaned up a lot of the code (thanks again, Stephane for the help!). You can download the new .zip containing the project from my website: http://homepage.mac.com/tylers/Development. I will be updating the site with the new code ASAP.

Quick question: the tableview currently allows empty selection (which the prefPane doesn't). I tried changing it in IB, but then it selects the first row by default. I tried fixing that by doing a selectRow in awakeFromNib and somewhere else that I can't remember now, but that didn't work. Any suggestions? I thought about intercepting the mouseClick, but I couldn't figure out where to implement it. Any help would be greatly appreciated!

TylerStromberg

