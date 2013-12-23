---
layout: page
title: AddingBindingsToCustomClass
---

I have read that to add a binding to a custom class (like a custom view) you can put code like this into the implementation file (.m):

    
+ (void)initialize
{
	[self exposeBinding:@"myBinding"];
}


However, when I do this, drag the .h or .m files into Interface Builder, the binding does not show up in the inspector.  Is there something I am missing?

----
In order for this code to actually be executed, you must make an IBPalette for your view.
----
I was afraid of that.  Every example that I saw of exposing bindings were accompanied with the creation of an IBPalette.  What is the least amount of code I could put in the palette for it to have my additional binding?

----

Somebody will no doubt accuse me of being inelegant, but if you're not in the mood for a lot of code, just add a -bind: message in your controller's -awakeFromNib method:

    
- (void)awakeFromNib {
	[myView bind: @"myBinding" toObject: self withKeyPath: @"myProperty" options: nil];

	/* ... */
}


----

Isn't it better to bind a view's property in the view's awakeFromNib, after all when we bind things in IB we don't tell that controller what is being bound to it. I think this is better suited to be being put in a view which has a generic id _controller outlet.

And no that code isn't 'inelegant' I use it all the time to avoid having to write an IBPalette for *every* view and control I write. If however you are writing say a framework or some other element that will be used very frequently or in a group of people then yes writing the palette is worth it.

----
The problem with putting the binding code in a view's code is that views are meant to be reusable, while controllers are generally throwaway. (Not always; Cocoa Bindings itself is a reusable controller layer.) The other problem is that a view binding to a generic controller doesn't know what the key path should be, whereas the controller always knows what to set on the view. Basically, MVC says only the controller should know about the other two; the view should have as little "thinking" as possible. But I think I agree with it being fine for non-framework projects, as long as it's something you can't already do in IB (i.e. subclass NSControl for a simple value binding since it's already set up). If the view is what you're releasing, though, remember that  "easier for the programmer" is very rarely a justification for "harder for the user". --JediKnil

