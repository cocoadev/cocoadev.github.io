---
layout: page
title: KeyInputWhileDragging
---



I am implementing spring loaded folders in my finder replacement, and would like to get the space bar presses sent to my app whenever any other app is dragging over me.  At present, I have it working after a delay by using the validateDrop messages in my tableView delegate, but I would like to support the classic finder style keyboard actions.

I just discovered after several attempts to ask to become the key and main window, that even if you ARE the key, main and front window, when you initiate a drag in one of your windows, you no longer get keyboard events.  It is as if the system which manages tracking the dragImage and sending the NSDragging messages around is swallowing them all up.

Does anyone know of a way to get keyboard events even during a local drag?  I assume if i can get that working, then getting input when I'm not key, will simply be a matter of self window] makeKeyWindow]

**
I ended up using a Carbon routine to do get this to work.  I would still love to get a Cocoa solution to this.
**

    
- ([[NSDragOperation)draggingUpdated:(id <NSDraggingInfo>)info;
{
	long KeyMap[4];
	GetKeys(KeyMap);
	if (KeyMap[1] == 512 && drag_target_row != NODRAGTARGET)
		[self springLoad: drag_target_row];
...

