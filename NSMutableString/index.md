---
layout: page
title: NSMutableString
---

See also: http://developer.apple.com/mac/library/documentation/Cocoa/Reference/Foundation/Classes/NSMutableString_Class/Reference/Reference.html

----

I'm working on a way to make NSMutableString Key-Value Compliant. This doesn't work:
    
- (NSString *)string
{
	return [NSString stringWithString:self];
}


And neither does this:
    
- (NSString *)value
{
	return [NSString stringWithString:self];
}

- (void)setValue:(NSString *)newValue
{
	[self setString:newValue];
}


Both ways end up with an infinite retain loop on NSCFString -- I think it's setString that's the problem...
How else am I supposed to set NSMutableString's string?

But I think I'm getting there. I'm trying to use an array of strings from the user defaults as a table column's data source (through the value binding on NSTableColumn), and I know it's probably easier to use a dictionary but I'm just that stubborn type of guy :)

I'll post the solution up here when I'm done.

- JediKnil

[Edit: I give up for now...if anybody knows how to do it, please tell me. I'm going to use the stupid NSMutableDictionary method until further notice... -JediKnil]

[Edit: For some reason I can't use DND without binding the content, so I have NSArrayController with traditional datasource methods on one side and user defaults bindings on the other...how strange! - JediKnil]

----

Why don't you just cast?

*I guess I could, but I didn't :) The real reason is because if I change my string, it shouldn't change everyone else's string (unless updated by the bindings). Besides, I don't think that's where the problem is...but if you find a solution, please post it up here. --JediKnil*

----

I'm not sure what casting Dan is referring to.. but casting in ObjC rarely does anything besides shut up warnings from the compiler.  It's hard to see how any cast could help.

Knil, I just checked, and NSMutableString already implements     setValue:  – you accidentally overrode a built-in method!  Very likely the     setString: method calls     setValue:, and there's your recursion.  Why don't you try using a jk prefix for your key.
----
Wow, I forgot about this page...anyway the final solution has been up on BindingTableColumnToArrayOfStrings, along with a few possible issues. If anyone thinks it should be moved here, go ahead; you're probably right but I'm wary about duplicating information. The solution is really an NSString category anyway. --JediKnil

