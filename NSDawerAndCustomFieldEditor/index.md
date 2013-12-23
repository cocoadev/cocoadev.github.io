---
layout: page
title: NSDawerAndCustomFieldEditor
---

I just started developing an app in Tiger.  It consists of a main window, an NSDawer that contains an NSTextField.  I'm providing a custom field editor with the - (id)windowWillReturnFieldEditor:(NSWindow *)sender toObject:(id)anObject NSWindow delegate method.

Whenever I first place focus in the NSTextField I get the following in the application's log:

    
2006-10-16 11:01:44.135 Piper's Freedom[1322] *** Assertion failure in -[_NSKeyboardFocusClipView _gatherFocusStateInto:upTo:withContext:], AppKit.subproj/NSView.m:3129
2006-10-16 11:01:44.135 Piper's Freedom[1322] Exception raised during posting of notification.  Ignored.  exception: non-positive window number
2006-10-16 11:01:44.138 Piper's Freedom[1322] *** Assertion failure in -[_NSKeyboardFocusClipView _gatherFocusStateInto:upTo:withContext:], AppKit.subproj/NSView.m:3129
2006-10-16 11:01:44.138 Piper's Freedom[1322] Exception raised during posting of notification.  Ignored.  exception: non-positive window number


This doesn't seem to affect anything but since it's a new application I'd like to start on as solid of a code base as I can.  I've searched the internet like mad to no avail.  Any ideas?

Update: I've played around with the focus as mentioned at http://www.cocoabuilder.com/archive/message/cocoa/2005/5/22/136648 but it hasn't changed anything.

SOLVED:

I'm not using a custom field editor anymore.  I've implemented the

- (BOOL)control:(NSControl *)control textView:(NSTextView *)fieldEditor doCommandBySelector:(SEL)commandSelector

method which gives me all I needed to know.

