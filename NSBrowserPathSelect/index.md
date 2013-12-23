---
layout: page
title: NSBrowserPathSelect
---



I have been hacking around for about four hours trying to get an NSBrowser to highlight the path that has just been set:

    
    [browser setPath:somePath];


My goal was to create a history behavior similar to the Finder (with back and forward buttons). In a finder window in browser mode, when you hit the back button the Finder will highlight the last path component in blue (e.g. if the last path was ~/Library/Preferences the Finder will highlight the cell "Preferences" blue). I created a browser delegate that records the path history of a finder like document and implements back and forward button actions. The browser will shade the correct path components gray when a history path is set, but which path component is highlighted blue is not always the last path component. Here's an example of a simple browser delegate that records the path history and responds to back and forward button actions:

    
- (IBAction)browserAction:(id)sender {
    if ([self setPath:[sender path]]) [self addPathToPathHistory:[self path]];
}

- (IBAction)backButtonAction:(id)sender {
    [browser setPath:[self previousPath]];
}

- (IBAction)forwardButtonAction:(id)sender {
    [browser setPath:[self nextPath]];
}

- (BOOL)setPath:(NSString *)path {
    BOOL isValidPath = YES;
    /*
          Do something here to validate the path;
    */
    return isValidPath;
}

- (NSString *)nextPath {
    if (pathHistoryIndex + 1 < [pathHistory count]) {
        if (pathHistoryIndex + 2 == [pathHistory count]) [forwardButton setEnabled:NO];
        [backButton setEnabled:YES];
        return [pathHistory objectAtIndex:++pathHistoryIndex];
    }
    return nil;
}
- (NSString *)previousPath {
    if (pathHistoryIndex > 0 && pathHistoryIndex < [pathHistory count]) {
        if (pathHistoryIndex - 1 == 0) [backButton setEnabled:NO];
        [forwardButton setEnabled:YES];
        return [pathHistory objectAtIndex:--pathHistoryIndex];
    }
    return nil;
}

- (void)addPathToPathHistory:(NSString *)path {
    if (pathHistory lastObject] isEqualToString:path]) return;
    if (pathHistoryIndex < [pathHistory count] - 1) {
        [[NSRange r = NSMakeRange(pathHistoryIndex, [pathHistory count] - pathHistoryIndex);
        [pathHistory removeObjectsInRange:r];
    }
    [pathHistory addObject:path];
    pathHistoryIndex = [pathHistory count] - 1; 
    [backButton setEnabled:YES]; 
    [forwardButton setEnabled:NO];
}



I didn't include the datasource delegate methods to keep this discussion simple. Like I was saying everything works fine, but the browser will not highlight the last path component blue the same way a mouse down action on a cell will force the browser to display the current active cell with a blue highlight. I tried everything from asking the browser to reload each one of its columns to sending a "set" message to the cell associated with the last path component. I also tried asking each column matrix to redisplay and many other combinations using methods defined by NSBrowser and NSMatrix. The only thing that worked was to send a mouse down event to the column matrix associated with the last path component:

    

    int row = [self rowWithTitle:self path] lastPathComponent] inColumn:columnIndex];
    [[NSMatrix columnMatrix = [browser matrixInColumn:[self lastColumnIndexForPath:[self path]]];
    if (columnMatrix  && row != NSNotFound) {
        NSRect frame = [columnMatrix cellFrameAtRow:row column:0];
        [columnMatrix mouseDown:[NSEvent mouseEventWithType:NSLeftMouseDown 
                    location:NSMakePoint(NSMidX(frame), NSMidY(frame))
                    modifierFlags:nil timestamp:nil windowNumber:nil context:nil
                    eventNumber:nil clickCount:1 pressure:1.0f]];
        [columnMatrix mouseUp:[NSEvent mouseEventWithType:NSLeftMouseUp 
                    location:NSMakePoint(NSMidX(frame), NSMidY(frame))
                    modifierFlags:nil timestamp:nil windowNumber:nil context:nil
                    eventNumber:nil clickCount:1 pressure:1.0f]];
        [columnMatrix setNeedsDisplay:YES];
    }


    lastColumnIndexForPath: and     rowWithTitle:inColumn: are custom methods. The rest should be self-explanatory. 

It seems like there should be an easier way to accomplish this, so please feel free to share any information related to this problem. --zootbobbalu

----

Have you tried     selectRow:inColumn:?

Also, as a suggestion for tracking the path history, you may want to try creating your own undo manager for managing changes in the path, and then using undo/redo for the back/forward buttons.

----

Yep, tried selectRow:inColumn (actually this message gets passed on/back to the delegate      browser:selectRow:inColumn:). When I implemented this delegate method I tried many different things with no success.

Your suggestion to use an undo manager sounds like a good idea. --zootbobbalu

----

OK, I built a mockup and I think I've figured it out. When you click the back/previous button, you set the path, but the browser is no longer the first responder (the button is). In order to get the "blue" highlighting the browser needs to be the first responder. So, in     backActionButton: and     forwardActionButton: you ought to add a line like     browser window] makeFirstResponder:browser] to make the browser first responder again.

----

Thanks!!

That works. I knew there had to be a better way. It seems that [[NSBrowser's implementation for     setPath: is kind of buggy if it highlights cells when the view is not setting itself as first responder. I thought that first responder status might be the cause, but the incorrect blue highlighting made me reason that the problem was elsewhere.  

Now I'm trying to use an NSUndoManager to handle the path history. Things work fine, except I need to figure out a way to skip the top of the stack if this item represents the current state. --zootbobbalu

----

OK, I figured out how to use an NSUndoManager for keeping track of the path history. Here's a sample of the code that provides a history for a Finder like browser:

    

- (void)awakeFromNib {
    if (!pathHistoryManager) pathHistoryManager = [[NSUndoManager alloc] init];
}

- (void)dealloc {
    [pathHistoryManager release];
    [path release];
    [super dealloc];
}

- (void)updateHistoryButtons {
    [backButton setEnabled:[pathHistoryManager canUndo]];
    [forwardButton setEnabled:[pathHistoryManager canRedo]];  
}

- (void)setPath:(NSString *)_path {
    if (![[NSFileManager defaultManager] fileExistsAtPath:_path]) return;
    if (path && ![_path isEqualToString:path]) {
        [pathHistoryManager registerUndoWithTarget:self 
                        selector:@selector(setPath:) 
                        object:path];
    }
    [self updateHistoryButtons];
    path = [_path retain];
    [browser setPath:path];
    browser window] makeFirstResponder:browser];

}

- ([[IBAction)backButtonAction:(id)sender {
    [pathHistoryManager undo];
    [self updateHistoryButtons];
}

- (IBAction)forwardButtonAction:(id)sender {
    [pathHistoryManager redo];
    [self updateHistoryButtons];
}



--zootbobbalu

