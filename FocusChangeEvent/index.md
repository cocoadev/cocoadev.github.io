---
layout: page
title: FocusChangeEvent
---

Does anyone know if there is a notification or delegate method that's called when a window changes focus from 'View A' to 'View B'? Thanks.

----

    
- (void)awakeFromNib {
	[[NSNotificationCenter defaultCenter] addObserver:self
		selector:@selector(viewFocusDidChangeNotification:)
		name:@"NSViewFocusDidChangeNotification"
		object:nil];
}

- (void)viewFocusDidChangeNotification:(NSNotification *)note {
    NSView *view = [note object];
    NSLog(@"<%p>%s: %@", self, __PRETTY_FUNCTION__, [view description]);
}


--zootbobbalu

