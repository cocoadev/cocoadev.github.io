---
layout: page
title: CCDAppKit
---

CCDAppKit contains some AppKit improvements for your enjoyment.

----
**Categories**

*NSBezierPathCategory
*NSFileManagerCategory
*NSMatrixCategory
*NSMenuCategory
*NSMenuItemCategory
*NSTextViewCategory


**Subclasses**

* CCDTextField, CCDTextFieldCell and CCDPTextView all go together. They allow you to embed a cell in the left and right hand sides of your text fields. Good if you want to do something like iChat's input field. It can also draw a progress bar behind the text like Safari's address field and has limited support for colored labels.
* CCDProgressIndicator is good for drawing rounded progress bars.
* CCDGrowingTextField is a simple NSTextField subclass that expands to fit its content.
* CCDGradientSelectionTableView attempts to mimic iTunes' "Source" list, example project included.


Feel free to add more here.

----
**plan:**

Create CCDSearchField and CCDSearchFieldCell, based on CCDTextField and CCDTextFieldCell.

----
Taken from CCDMessageDistributer, *I do think that it was presumptuous of me to use the CCD prefix for cocoadev. In fact thinking about it, that's more likely to cause a namespace conflict than anything else. And who decides what goes under CCD?*

I've sort of been thinking along the same lines. I'm not sure how it's likely to cause a namespace conflict though. And I think everyone decides what goes under CCD, I have faith in the wiki process. If it doesn't belong it'll rot and I'm ok with that. Is that the right attitude to have?

----

The one thing that I (remain) concerned with is that we not unnecessarily fragment the code contributions of this wiki.  I hope we will eventually wrap in code such as the CocoaDevUsersAdditions and such.  There's already some problem with this.  Look at the bottom of CocoaOpen and try to figure out how it's different from ObjectLibrary.
----
I don't see why the "Components" section of CocoaOpen shouldn't be in ObjectLibrary, if it's not already. And some of the stuff in ObjectLibrary should be in CocoaOpen I bet. I think the purposes of the pages overlap in some cases is all.

Anyway, I think fragmentation could be avoided with a little reorganizing. I was actually thinking about mirroring the AppKit section of CocoaDevUsersAdditions here and the foundation section on (err..) CCDFoundation.

Any objections or volunteers?
----
It would be cooler if it wasn't done manually but it's a start. Should I do the same on CCDFoundation or should I create CCDFoundationKit?

