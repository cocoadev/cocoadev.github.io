---
layout: page
title: ReloadDataNotWorking
---

Hello all,

I am new to Cocoa programming (and to this site) so please be gentle.

As an exercise, I wrote a Sudoku-solving program.  It uses an NSTableView which I set up in Interface Builder to show a headerless, 9x9 view with number formatters.  I have an AppController object which acts as the dataSource for the NSTableView, and which holds a 2D array of data (I know about bindings - I just wanted to do this exercise "manually").  In Interface Builder, I gave each table column an identifier (the numbers 0 - 8).  In tableView:objectValueforTableColumn:row I do the following:

    
-(id)tableView:(NSTableView*)aTableView
objectValueForTableColumn:(NSTableColumn*)aTableColumn
		   row:(int)rowIndex
{
	NSString *identifier = [aTableColumn identifier];
	int colIndex = [identifier intValue];
	if (m_values[rowIndex][colIndex])
		return [NSNumber numberWithInt:m_values[rowIndex][colIndex]];
	return nil;
}


(I don't really like this hack to get at the column number - any suggestions there?)

I also implemented tableView:setObjectValue:forTableColumn.  Everything works fine so far.

I wanted to implement loading and saving of the sudoku grids, and decided to do so using "plain" C code.  However, when I read in the values and change my     m_values array and do a     [m_gridView reloadData];, the grid is not updated on screen.  When I select a row in the table, it is immediately filled with the correct (new) data, but I would expect that the reloadData would trigger a redraw automatically.

I tried adding     [m_gridView setNeedsDisplay:TRUE]; and even     m_gridView window] displayIfNeeded]; but to no avail.

I saw the article [[NSTableViewNotReloadingCorrectly on this site, but that was related to not being a dataSource.  I have no idea why my NSTableView is not redisplaying the correct values automatically.  All hints are welcome!

----
When are you calling reloadData? If it's in your initializer, it's too early. The outlet isn't connected yet, so the call will do nothing. See AwakeFromNib.

As for suggestions about your hack, I'd suggest that it's an indication that you shouldn't be using NSTableView at all. NSTableView is meant to manage a set of potentially resizable and rearrangeable columns displaying data for an arbitrary number of rows. It's not all that suited for a straight 9x9 grid. NSMatrix will probably be easier to work with there.

----
I'm calling it in my     open: function, which I connected as the target of the "Open..." File menu.  The rest of my app works fine.  It also doesn't matter whether I first enter a few numbers manually and then load a file.  It simply won't redraw unless I force it to by selecting table rows.

As to the NSTableView: I first laid out 9x9 text input fields but quickly decided this wasn't the way to go.  Like I said, I'm new to Cocoa and the whole Interface Builder way-of-working takes some getting used to.  In a "programmatic" way, I'd simply declare a 9x9 array of input fields in my code, and assign them to the corresponding array elements in code.

I've read about NSMatrix (I've worked through the Hillegass book (2nd edition) where it's mentioned) but haven't seen anything in depth about it.  I can try making an NSMatrix of NSTextFieldCells, but I would still have to "wire up" all 81 cells manually, right?

Anyway, I'd still like to find out what the root cause of this reloadData failure is -- knowing why something doesn't work is as important as knowing what does :-)

----
No, you wouldn't have to wire up 81 cells manually. I suggest you read the documentation for the class.

----
It turns out the     m_gridView wasn't connected to the NSTableView in Interface Builder.  I still need to get used to the fact that things like this don't simply crash.  Neither the      [m_gridView setNeedsDisplay:TRUE]; nor the     m_gridView window] displayIfNeeded]; were necessary after that.

In the mean time I have re-implemented the GUI using an [[NSMatrix, despite the cynical comment regarding its documentation above.  I found that it sends a notification once a cell has changed its contents with the cell in its userInfo.  I haven't been able to retrieve that cell's (x, y) position yet from the NSMatrix (it returns (-1, -1) for it).

