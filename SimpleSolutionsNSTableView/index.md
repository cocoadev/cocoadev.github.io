---
layout: page
title: SimpleSolutionsNSTableView
---

 (Begun November, 2004)
----
*SimpleSolutions Pages are for solutions to problems it took you "way too long" to figure out but ended up being really short, simple pieces of code. Whether or not it would take someone else a long time is beside the point. Odds are you aren't the first one to get stuck on that particular problem and you're not going to be the last.*

----
**This page is for NSTableView related solutions only**.
----

Feel free to add to this page. *Short*, **tested** code is best. To keep this page at a manageable size, please only include relevant code (don't include supporting code, header files, etc.). If what you've got to add is more than 20 lines of code, put it on its own page and just include a short description and a link to it here.

Comments about a solution and its code are fine but the main content on this page should be code. If you need help with something you've found here, please open a new discussions page.

----
**Table of Contents:**

*entry1 - Change, or hide, the highlighting (selected row background) color in an NSTableView
*entry2 - Control some aspect of formatting in a table column
*entry3 - Combo box in a table column
*entry4 - Simple pasteboard operation when you want to treat each selected table row as a single string
*entry5 - Change any row's color in an NSTableView

----


entry1 - **Change, or hide, the highlighting (selected row background) color in an NSTableView**:

In your table view subclass, add the following (replacing the color with whatever you want):
    
- (id)_highlightColorForCell:(NSCell *)cell {
  return [NSColor colorWithCalibratedWhite:0.9f alpha:1.0f];
}

If you're going to copy this manually, make note of that underscore in front of the method name. 

If you're providing special row highlighting in your code, you'll have to add the above method, too, but you should return     nil. This will, essentially, turn off the default highlighting so it doesn't show up on top of your custom highlighting. (Tested in 10.3, Xcode 1.5)

Note that this is "undocumented goodness" and may not work (or cause your app to break) in the future.

----
entry2 - **Control some aspect of formatting in a table column:**

Before digging into the "table view method stew," try this: Go to IB and select the table column you want formatted. (The Header Cell has to be visible for this to work. If it's hidden, unhide it and after you're done, you can hide it again.) In the Tool Palette's toolbar, select the "Data" tool set. In the lower left there you'll find "formatters" for images, text, etc. Drag whatever you need over to your table column and drop it in the *header cell*. A little triangle will appear in the upper right corner. Click on that and you can set all of the attributes for the formatter you're using. (Tested in 10.3, Xcode 1.5)

----
entry3 - **Combo box in a table column:**

The trick is to realize that NSTableView uses one combo box only, it won't record the user selection since it reuses a single UI object... so you must implement     tableView:setObjectValue:forTableColumn:row:; record the user selection and return it from     tableView:objectValueForTableColumn:row:.

See NSComboBoxCellExample for a more comprehensive example.

----

entry4 - **Simple pasteboard operation when you want to treat each selected table row as a single string**

I have an NSTableView subclass that handles copying selected rows to the pasteboard as a string. The code runs from the     copy: method, and works fine. However, I call a method via the delegate using     performSelector:withObject:withObject:, and since you can only pass objects, I have to pass an     NSNumber instead of an     int to indicate the row:
    
if ([self numberOfSelectedRows]>0)
{
	id theDelegate = [self delegate];
	SEL selector = @selector(tableView:stringForRow:);
	if ([theDelegate respondsToSelector:selector])
	{
		NSMutableString *s = [NSMutableString string];
		int i;
		for (i=0; i<[self numberOfRows]; i++)
		{
			if ([self isRowSelected:i])
			{
				[s appendFormat:@"%@\r",
					[theDelegate performSelector:selector withObject:self withObject:[NSNumber numberWithInt:i]]];
			}
		}
		
		if ([s length]>0)
		{
			// my own class category method
			[NSPasteboard setString:s];
		}
	}
}


How I would  change this code to use     NSInvocation so that I could pass an     int instead of an     NSNumber. Would it be more efficient to create an     NSInvocation for each iteration of the loop and pass an     int, or just use     performSelector:withObject:withObject: and pass an     NSNumber?

----

For your code, you already know the selector. It's     tableView:stringForRow:. Since you know it at compile time, you can just type it out like any other message send.

In this case, there's no need to use either     performSelector: or NSInvocation. Just send the message thusly:

    
[theDelegate tableView:self stringForRow:i]


----

entry5 - **Change any row's color in an NSTableView**

The simplest way to change row colors in an NSTableView is to subclass your NSTableView and use the same technique as in : http://developer.apple.com/samplecode/MP3_Player/index.html . Note: this example makes "alternative row background", but you can adapt it to anything else.
 Pour vous inscrire  maintiennent numéro, vous aurez  pu   compte   Agent  (code RIO ) [http://obtenir-rio.info numéro rio]. Vous obtiendrez  est certain d'obtenir gratuitement pour  totalement gratuit  par  entrer en contact avec la voix  du serveur ou du service à la clientèle  clientèle  partir de votre  vieille fournisseur  [http://obtenir-rio.info/rio-bouygues rio bouygues] . Vous ne  mai   get un SMS  avec vos . Avec  votre propre  [http://obtenir-rio.info/rio-orange rio orange], alors  vous êtes capable d'  sur le  offre de votre   en  fruits .

