---
layout: page
title: NullPlaceholderWhenUsingNSFormatter
---

I want to display a date nicely, one from my user defaults (last time my application performed a software update) so it may be NULL, in which case I want to show a placeholder string. How can I do that (only using bindings) when I've attached an NSFormatter to the text field which displays the date?

Solution: Create two text fields, one with the formatter, the other having the placeholder string. Bind the isHidden to the date in the user defaults and use the NSIsNil and NSIsNotNil value transformer.

----

I don't really like that solution.

What I would do is use registerDefaults: on NSUserDefaults (see Apple's documentation at http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Objc_classic/Classes/NSUserDefaults.html ). This allows you to set a value for the date beforehand...which might be more helpful than a placeholder. Today's date, for example. Just put the call to registerDefaults: in a short awakeFromNib method on ANY of your nib objects, or anywhere else that will be called before or during the nib loading. While this doesn't stick to pure CocoaBindings, it probably represents a cleaner solution.

----

The task is to have it show either the date from the user defaults, or if there is none, a textual description to why there is no date. So registering a default date would not give a textual placeholder.

----

Bindings should be used to reference a single value. Imagine doing this for a table of date values...it would never work! If you really want a description, a custom NSFormatter would probably serve your preferences. Just subclass NSDateFormatter and override stringForObjectValue: to check for nil. Something like this (untested):
    
- (NSString *)stringForObjectValue:(id)obj
{
    if (obj == nil) return @"The date won't show up because it is not in user defaults.";
    return [super stringForObjectValue:obj];
}

--JediKnil

----

Other than having to write code your solution also then causes some strings to be in code (thinking about localization here) and I don't think it's actually possible to attach a custom NSFormatter subclass to a text field in the current version of InterfaceBuilder. So I'd have to create outlets/instance variables and instantiate/attach the formatter in code!?!

----

It is possible to use a custom subclass of NSDateFormatter in IB.  Drag NSDateFormatter from the palette to the top level nib objects window to instantiate it, then drag from the text field to the formatter to set the "formatter" outlet.  Select the formatter object to set its custom class and format string.

Besides, localizing strings is really easy. Having two *different* text fields just so that you can include a default value is just not good style. IB works with custom objects all the time. Even though the functionality isn't built-in, it's still a lot easier than code, and better for long-term effects than double text fields. Don't use bindings just to avoid coding.

----

Actually, what I do is just a workaround for the fact that the �Null Placeholder� doesn't work when using a formatter. The workaround is rather simple.

From the *Don't use bindings just to avoid coding* it sounds like you adhere to some abstract directive (i.e. *when* should one not use bindings? 

To cope with complexity, redundancy should be minimized.

Rather than constrain the GUI to a single nib, you introduce a new Cocoa sub class (two files), the Nib depends on the header for this and you introduce a new string to the localization table.

