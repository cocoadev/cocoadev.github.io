---
layout: page
title: KnownBugs
---

**List of Known Cocoa Bugs**


*People should probably post Apple Bug ID#s here so others can reference them in their own bug reports.*

----

Title: mouseMoved events not always received

Added: March 21, 2002

Resolved: No (as of March 22nd, 2002)

Description: NSView mouseMoved events can only be enabled/disabled when the NSView is the FirstResponder. Furthermore the events are only received when the NSView is the FirstResponder.

----

Has this been resolved in Jaguar? I am using 10.1.5 and PB 1.1 and even though I set my NSView subclass as initialFirstResponder of the window it's in it doesn't get any mousemoved events and yes, I have set acceptsMouseMovedEvents:YES

Any ideas?

/Gabriel Falkenberg

----
Slightly random and unlikely to help: are you setting acceptsMouseMovedEvents too early? ie before the view is made first responder? -- KritTer

----

I thought I had but maybe I hadn't. Anyway after placing this code in my view it works as expected:

    - (void)awakeFromNib
{
	self window] makeFirstResponder:self];
	[[self window] setAcceptsMouseMovedEvents:YES];
}


Note that this is using Jaguar but I guess 10.1 will do. --Gabriel

----

[[NSImage cant read 1bit (Black and White) BMP files.
-- mark Horner mark@mhsb.co.uk

----

When viewing an NSString that starts with a plus sign ("+") in GDB, it comes up as an empty string. See NSStringInconsistanciesInDebugger.

-- MarcWeil

----

NSString's stringByTrimmingCharactersInSet is wacked out.  See http://developer.apple.com/qa/qa2001/qa1202.html .

Fixed in Panther.

----

NSLog does strange things to backslashes in strings.  For a workaround you can print out cStrings instead.

*does strange things* - can you be more specific?

----

FirstResponderSetAfterDelay

NSDictionaryAndNSOutlineViewBugs

----

Drawer content size bug in Interface Builder - prevents you from changing the size of a drawer after changing the drawer's parent window size.  See the following web page for details:

http://www.geocities.com/rjpoling/MacOS/devnotes.html

