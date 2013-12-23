---
layout: page
title: DynamicallyLoadFrameworks
---

an example for loading frameworks dynamically:

    NSString *frameworkPath = [[NSBundle bundleForClass:[self class]] pathForResource:@"ObjCOSC" ofType:@"framework"];

NSBundle *OSCframework=[NSBundle bundleWithPath:frameworkPath];
[OSCframework load]; 

----

Note that in a standard application, the above is unnecessary.  Frameworks will load automatically.  It might be necessary to use such an incantation if you include a framework within a bundle.

*There are situations where one would load a framework dynamically from an application, and this could be useful for those times. It is fairly rare.*

Also, using     [self class] as an argument for     +[NSBundle bundleForClass:] isn't a great idea.  It doesn't retrieve the intended bundle if the 'self' object is a subclass of the intended one and wasn't loaded from the same bundle.  It would be interesting, for example, to check what happens with the dynamic subclassing used in KeyValueObserving.

*A very good point. KVO goes through pains to remain invisible, and I wouldn't be surprised if they overrode     -class to return your class anyway. But even so, subclassing will break this in other circumstances even if KVO doesn't.*

----

One example of where this technique is useful is in PlugIns that want to use a third-party framework that is not loaded by the host application. Before loading, you should, however, test that the symbols in the framework have not already been loaded (this can be done with     NSClassFromString for example). --TheoHultberg/Iconara

----

I want to use the PDFKit framework in my app, but I only want to use it for Tiger users. It would only add an additional feature to the app, so it's not necessary for the app to run. How would I load the PDFKit framework dynamically and correctly for Tiger users, but still allow the app to run for 10.2-10.3 users (the code that calls PDFKit is already in a plugin, but comes bundled with the app)? Thanks.

----

I wanted to do something like this for one of my apps (link the FScript framework only if it was available) and found that the -weak_framework flag for the linker did the trick.  In my project, I just added "-weak_framework FScript" to the Other Linker Flags field for the app target.  Hope this works.

*Great thanks! I was looking around into some of that weak linking, but wasn't sure how to actually use it the right way.*

----

I don't believe you can weak link Objective-C classes, although that technique should work if you refer to the classes using NSClassFromString instead of putting them directly in the code.

----

That's what I did. Instead of using:
    PDFDocument *doc = [[PDFDocument alloc] initWithData:data];
I used:
    Class pdfDoc =  NSClassFromString(@"PDFDocument");
if (pdfDoc == nil) return;
id doc = pdfDoc alloc] initWithData:data];

*Just as a style note, the *correct* way (according to Apple) to refer to "no class" is     Nil. However,     Nil is defined as     (Class) 0 and     nil is     (id) 0, so it doesn't really matter. --[[JediKnil*

Still, just     if (!pdfDoc) return would be more correct in this case

*A class is also an object, so nil is also correct; Nil is just more specific.*

