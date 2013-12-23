---
layout: page
title: CocoaDialogs
---



Are dialogs sheets?

*No.*

And is it possible to have a dialog within a dialog?

*No.*

:-)

  Sorry to be terse, but without knowing what you're trying to do, I can't really elaborate. This topic may interest you, though: http://www.cocoabuilder.com/archive/message/cocoa/2005/3/30/131753

----

ok. What are dialogs then? :-)

*There are alert panels (check out NSAlert documentation) that float above like a windows dialogue box and sheets (check out NSApplication's documentation, the beginSheet:modalForWindow:modalDelegate:didEndSelector:contextInfo: method) which are panels that pop out of the top of whatever window they're attached to.*

