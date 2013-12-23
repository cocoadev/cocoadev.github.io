---
layout: page
title: HowToGetAnNSTableViewToDisplayAnImageInItsCells
---

I have been trying to put images into the cells of one column of an NSTableView, but have had no success.

Here is my setup.  I am trying to make a simple multiple-choice test editor.  Each question is an object and
represents a row of an NSTableView.  Each question has a column for the question, a column for each of the
four possible answers and a column for the number of the correct answer. The NSTableView is bound to an
NSArrayController whos "content array" is linked to an array of "Question" objects.  This array is called "test".

I have two buttons below the NSTableView with a plus on one, and a minus on the other.  When you press
the "plus" button, a "question" object is added to the array.  So far, all of this works perfectly.

Now for the problem. I would like for one of the columns, named "Diagram" to hold an NSImage associated
with the question.  In Interface Builder, I have made the column "editable".  I also draged an "NSImageCell"
into it so that it is formated properly.  When I make an NSImageView *editable", I can drag an image file into
it without any code. I was hoping to be able to do the same thing with the cells of the column.

When dragging and dropping an image file into the cells did not work, I tried to put this code in the
 -(void)awakeFromNib method of the myDocument object.  The code is supposed
to format the "Diagram" column to take NSImage's.

    
NSTableColumn *tableColumn;
NSImageCell *imageCell;
tableColumn = [tableView tableColumnWithIdentifier:@"Diagram"];
imageCell = [[NSImageCell alloc] init];
[imageCell setEditable: YES];
[tableColumn setDataCell:imageCell];
[imageCell release];


I would like to see the image that I drop go into the appropriate cell of the "Diagram" column.  Any
ideas why this is not working?  Thank you in advance for any help! -AlexanderD

