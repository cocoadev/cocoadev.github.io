---
layout: page
title: ChangingAnAttributeInItsSetter
---



Can someone help me with the following problem?

I have a textView's attributedString bound to an arrayController's selection.syntaxColoredString attribute. This arrayController is bound to a couple of objects from a custom class named JHFormula. What I want to do, is update the syntax coloring each time the string has been changed in the textView. One way to do this, is by making a certain controller object the textView's delegate and apply the syntax coloring in its textDidChange method. But instead of doing it this way, I want to let the JHFormulas do the syntax coloring theirselves, because that way they can efficiently make use of their own data. Now, the problem: The logical place to make changes to syntaxColoredString (namely syntax color it) is in the setter, but if I do this, the textview doesn't observe, because itself has already called the setter. Of course calling the setter from the setter also is no solution. Does anyone know how I can get the textView to know that it has to observe the syntaxColoredString attribute? I understand that the observeValueForKeyPath:ofObject:change:context: message is a very likely candidate to help me out, but how does an JHFormula know to which object to send it? NSObject must have a way to know which objects are interested in changes of certain properties, but I can't find out how to use this...

----
Don't put view stuff (syntax highlighting) in your model (JHFormula).  Since syntax highlighting can be perfectly and reproducibly derived from the formula itself, there's no need for the formula to even store it.  So no, the logical place to make changes to syntaxColoredString is exactly nowhere, because you're not using bindings correctly.

What you should do is create an NSValueTransformer that takes a JHFormula and outputs an NSAttributedString.  Then, bind the NSTextView's value to MyArrayController.selection, and use your NSValueTransformer.  You'll probably want to make it a two-way transformer, and on the inbound direction convert the NSAttributedString to a plain-text NSString, which your JHFormula will then convert into whatever necessary internal representation in its setter.

