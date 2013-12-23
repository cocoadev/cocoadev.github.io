---
layout: page
title: GettingADocumentsManagedObjectContext
---

How can a custom view get the managed object context for its document from within a document-based application?

I would have thought that the following code would get the managed object context.  It is being used from within a custom view.

Line 1:      NSManagedObjectContext *moc = [self managedObjectContext];

The error I get when using the above code is:

Line 2:      [myCustomView managedObjectContext]: selector not recognized [self = 0x36d3f0]

When I use the following code:

Line 3:      NSManagedObjectContex *moc = [[NSApp delegate] managedObjectContext];

moc ends up being nil.  As far as I understand, Line 3 is good for non document-based applications where there is just one managed object context, but in a document-based application there can be a managed object context for each window (document).  This is why I thought there should be a way for a custom view from within a window to ask for the managed object context that is used for that document.  Hence, my creation of Line 1.  What is the correct way of doing this?

The other problem that could exist is that the code in the custom view is asking for the managed object context before it has been loaded.  If this is the case, how do I get around this?

----

ModelViewController would seem to suggest that your view shouldn't be doing this. Your controller, either the NSDocument itself, or an NSWindowController, should be dealing with all of the MOC stuff and handing off the appropriate bits to your view so it can draw.

----

Sounds great.  So, how do I get the values stored in core data (the reason for trying to get the managed object context) into the drawRect method of my custom view?

----
Like the anonymous poster said above, you don't, not directly. Instead, you take the NSControl-type approach, in which your view has a value and you query *that* for your information. Then you can set that value manually or use CocoaBindings to get data out of the managed object context. (And just as a note, you're probably getting     nil from Line 3 because you never set your application's delegate.) Anyway, you should understand that in ModelViewController, the view doesn't know anything about how the data gets there (it should never know if you're using CoreData or saving to binary files or just grabbing values from a text field)...any translation should be in the controller layer, which is separate. Sometimes it's not entirely possible to get pure MVC, but you should never actually use anything beyond your view's (or really, your control's) value. Subclass NSControl instead of NSView. --JediKnil

----
Thank you JediKnil for looking into this.  Ok, so I do not access core data directly in the custom view.  So, how do I access it indirectly?  You mention using cocoa bindings.  That is how the user (in my application) is interacting with the data from core data using tableviews, buttons, and an nsarraycontroller.  It seems well designed for tableviews, but what if my custom view needs to know some of the information stored in core data.  Custom views (as they are) do not have bindings that allow for it to keep up with the information that the nsarraycontroller has.  Do you know of a way of adding such bindings so that it could have access to the core data through the nsarraycontroller?  Consider the custom view like a space that will automatically draw the points that are stored in the nsarraycontroller.  As you add entities (using the tableview), points automatically get drawn in the custom view.  That is why I wanted it to be custom view and why I wanted it to have access to the data stored in core data and perhaps piped through the nsarraycontroller.  I hope I am making sense and that there is an easy way to do what I want to do.
----
I have created several complex views which graphically represent inter-related objects.  The first thing to remember is that objects are objects.  It shouldn't matter to the view how the objects are represented or stored.

I usually give the view an outlet called dataSource.  I create a formal or informal protocol for the data source.  Sometime early in initialization or when loaded from a nib, the view's dataSource outlet is set.  From that time on, when the view is asked to draw, it simply asks its data source for the objects to draw.  Remember, the data source can be any object that conforms to the correct protocol.  The data source should probably be in the Controller layer of MVC; NSDocument or NSWindowController subclasses are likely data sources.  If the data source is implemented to retrieve the model objects via Core Data, that is fine.  If the data source creates objects on the fly or uses some other technique, that is fine too.  The View has no need to know where the model objects come from.

I sometimes implement view specific categories that add methods to the model objects supplied by the data source.  For example, I might add a -drawWithOpenGLContext:inFrustum: method to of the model classes, but the method is implemented in a category that is defined in the View layer of MVC and more specifically in the OpenGL View.  Another type of category within the View layer might add -drawWithQuartzInRect:.  This way, view specific code is implemented in the View layer.  Different types of View can use the same model objects provided by the same data source.  The Model has no dependency on the View.  The View's only Model dependencies are implemented within the View itself.

