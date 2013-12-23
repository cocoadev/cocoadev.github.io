---
layout: page
title: EditingNSCellSubclassWeirdness
---

I have a tableView, which I have subclassed in order to draw a gradient selection background. I then have a subclassed NSCell which is very similar to the example "IconAndTextCell" that Apple provides. My tableView's dataSource method queries an NSMutableArray, and then returns values from it. Currently, when I edit my subclassed NSCell, it works as expected for the first edit. From then on, every time I edit a cell, every cell that I have edited up to this point changes its text to reflect the current cell's new title. Does anyone see anything in this code that might be causing this?

In my tableView subclass, I have this, in order to prevent the tableView from selecting the next row when I finish editing.
    
- (void)textDidEndEditing:(NSNotification *)aNotification
{
    //NSTableColumn *secondCol = self tableColumns] objectAtIndex:1];
    //[self selectRow:([self selectedRow]-1) byExtendingSelection:NO];
    //[super textDidEndEditing:aNotification];
    if ([[[aNotification userInfo] objectForKey:@"[[NSTextMovement"] intValue] == NSReturnTextMovement) {
        // This is ugly, but just about the only way to do it. NSTableView is determined to select and edit something else, even the text field that it just finished editing, unless we mislead it about what key was pressed to end editing.
        NSMutableDictionary *newUserInfo;
        NSNotification *newNotification;
        
        newUserInfo = [NSMutableDictionary dictionaryWithDictionary:[aNotification userInfo]];
        [newUserInfo setObject:[NSNumber numberWithInt:NSIllegalTextMovement] forKey:@"NSTextMovement"];
        newNotification = [NSNotification notificationWithName:[aNotification name] object:[aNotification object] userInfo:newUserInfo];
        [super textDidEndEditing:newNotification];
        
        // For some reason we lose firstResponder status when when we do the above.
        self window] makeFirstResponder:self];
    }
}



And in my [[NSCell subclass, I have this:
    
- (void)endEditing:(NSText *)textObj
{   
    NSMutableArray *layersArray = [[NSMutableArray alloc] init];
    layersArray = [[[NSDocumentController sharedDocumentController] currentDocument] layersArray];
    int row = self controlView] selectedRow];
    int indexToQueryForTitle = row * 5; 
    [layersArray replaceObjectAtIndex:indexToQueryForTitle withObject:[textObj string;
    self controlView] reloadData];
    [super endEditing:textObj];
}

In case anyone is wondering about this line: int indexToQueryForTitle = row * 5; I have other information about each row stored in the array too, so this finds the index to store the title in.

Does anyone see a reason why every cell I have edited up to this point would be edited to have the new title?

Also, as you can tell, my array is stored in [[MyDocument because the values and size of the array changes based on which document is in focus. Unfortunately, this throws up some warnings when I compile. Is there a better way to reference this NSMutableArray?

-- MattBall
----
Well, two things pop out at me. First, why can't your table view's data source handle the text update in     tableView:setObjectValue:forTableColumn:row:? (If it's because of the     textDidEndEditing: hack, I can't think of an easy way to fix it...) Controls should not (in general) manage their own models. Probably your main problem is the     selectedRow, which isn't always that reliable (in my experience). Secondly, there is no need to create an empty NSMutableArray just to reference the other one. In fact, this code is leaking memory because of it. Either take out "     = [[NSMutableArray alloc] init]" (but leave the semicolon), or move the entire assignment on the second line into the declaration. (Shown below) Good luck... --JediKnil
    
NSMutableArray *layersArray = [[[NSDocumentController sharedDocumentController] currentDocument] layersArray];

----
Thanks, that worked perfectly. That was one of the first things that I tried when this problem cropped up, but it made things terribly worse. I suppose something I changed in my search of the problem fixed it. And thanks for the heads up about the memory leak. The only problem I'm still having is that my mutableArray reference is still giving some warnings. If anyone has any insight into that, that would be great. In MyDocument, I've got a reference in the header and the main file to return the array, which I thought would fix it, but it didn't.

-- MattBall

*I think I figured it out...    currentDocument doesn't know what type of document is being displayed, so it will return an NSDocument. You, of course, know that it is an instance of My<nowiki/>Document, but the compiler doesn't. And there's your warning. --JediKnil*

So there's no easy way to fix it? As long as it's not a problem, I suppose I can just ignore it...
-- MattBall

*The only real way to fix the warning is to add a cast to the code, like so, although the second example might be clearer. --JediKnil*
    
NSMutableArray *layersArray =
    [(MyDocument *)[[NSDocumentController sharedDocumentController] currentDocument] layersArray];
// This is simpler
MyDocument *current = (MyDocument *)[[NSDocumentController sharedDocumentController] currentDocument];
NSMutableArray *layersArray = [current layersArray];

----
Alright, I'm back with some more editing weirdness... I was working on fixing some memory leaks, and realized that I never implemented NSCopying in my NSCell subclass. When I implemented it, my cell editing went to hell. When I double click to edit the cell, the cell's background turns white, and the text is selected, but I can't edit or reselect it. Also, I should mention that the entire cell should not be white. Only a small area around the text should have the FieldEditor. Here's my code:

    
- (id)copyWithZone:(NSZone *)zone
{
	LayerCell *copy = [self class] alloc] init];
    [copy setImage: [self image;
	return copy;
}

// Reset the text color to account for the cell being selected

- (NSText *)setUpFieldEditorAttributes:(NSText *)textObj
{
	[self setTextColor:[NSColor textColor]];
	return textObj;
}

// Modify the editing frame to compensate for the thumbnail image

- (void)editWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject event:(NSEvent *)theEvent
{
	if([self image] != nil) {
		NSRect textFrame, imageFrame;
		NSDivideRect (aRect, &imageFrame, &textFrame, 10 + self image] size].width, [[NSMinXEdge);
		textFrame.origin.y = NSMinY(textFrame) + textFrame.size.height/4 + 2;
		textFrame.size.height = 18;	
		[super editWithFrame:textFrame inView:controlView editor:textObj delegate:anObject event:theEvent];
	}
	else {
		[super editWithFrame:aRect inView:controlView editor:textObj delegate:anObject event:theEvent];
	}
}

- (void)selectWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject start:(int)selStart length:(int)selLength {
	if([self image] != nil) {
		NSRect textFrame, imageFrame;
		NSDivideRect (aRect, &imageFrame, &textFrame, 10 + self image] size].width, [[NSMinXEdge);
		textFrame.origin.y = NSMinY(textFrame) + textFrame.size.height/4 + 2;
		textFrame.size.height = 18;
		[super selectWithFrame:textFrame inView:controlView editor:textObj delegate:anObject start:selStart length:selLength];
	}
	else {
		[super selectWithFrame:aRect inView:controlView editor:textObj delegate:anObject start:selStart length:selLength];
	}
}


Now, selectWithFrame: IS getting called, but it doesn't seem to be having any effect.

-- MattBall

Okay, I resolved it, thanks to the kind people on the Cocoa-Dev mailing list. It was an issue with my copyWithZone: method.

-- MattBall

