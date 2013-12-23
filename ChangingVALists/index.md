---
layout: page
title: ChangingVALists
---

Does anybody know how to modify a va_list.  And in particular, how to append (or insert) items to an already existing va_list?

----

I don't think it can be done, at least not without a lot of hacking. va_list doesn't know anything about its contents, it trusts the function that receives it to know what kind of parameters there are and how many. What are you really trying to do?

Can't be done.  The va_list references a chunk of bytes on the stack,  On top of those bytes are the local vars and stack frame for the current function.  If you add anything to that va_list, you'd clobber other stuff on the stack.  What is it you're trying to accomplish?  There may be a better way.

I'm trying to pass a va_list passed into my method directly on to another method -- only problem is that I loose the first element of that list... I can always feed in a leading dummy. but the thought of that just hurts.

You always lose the first element of the va_list passed in? Are you getting a va_list passed in explictly (like if you were vfprintf()), or are you the one in the "..." function that's making a va_last to pass on to someone else?

I have a method that looks like:      - (void)myMethod:(id)arg1, ...   Then, I want to pass arg1 and the va_list over to something like      - [NSString initWithFormat:(NSString *)format arguments:(va_list)args] , which doesn't work so hot, because I miss arg1, which although expected, is not the desired effect :(

How are you constructing the format?  Are you walking the va_list to see what's in there, then building the format string?  If not, can your method take the format string in addition to the arguments?

No -- the method generates the format string, which is why I can't pass it in.  I may simply be screwed and have to do this in some hackish way -- I can't believe there's no way to copy the va_list out of the stack do something to it, and then pass that on when opening a new frame... this is really the first time I've felt so stylistically limitted by (obective) C in such a big way.

Yeah, it sounds like you're stuck :-(  Either the first method needs a first argument (even if it is a dummy), or the second method needs to take an array or something which you build from the va_list.  Unfortunately doesn't look like there's an NSString format method that takes one.

*Incidentally methods actually do take two hidden arguments. The object (    self) and the selector (    _cmd). It's probably a bit hacky, but you might successfully declare the va_list using     _cmd as the argument.*

----

The varargs macros are very limited. When your function is called, the computer has no idea how many parameters there are, or of what type. You can't copy or modify a va_list because the computer actually has no idea what is *in* the va_list; it relies on your function knowing about the contents (because of a e.g. format string like     printf(), or knowing in advance like     arrayWithObjects:) and properly extracting them.

Looking at stdarg.h, it appears that va_list is actually an internal compiler structure. If you're really determined to make this work, you'll have to find out what this structure is, and figure out how to do evil with it.

Another thing you could try is to initialize your va_list with the argument *before*     arg1, which would be     _cmd in this case. This is probably not supported, but it might start out by making the first argument of your method be the first argument in the va_list.

----

As the OP, I've come to wonder about the appropriateness of va_Lists, and if they are antiquated, in the face of NSArrays and NSDictionaries.  I appreciate that C arrays are no substitute; dynamic memory allocation would not be feasible, but in cases where the overhead of an NS data type is acceptable, I think I'm going to start doing that instead of va_lists.  Any comments as to why this is or isn't a good practice to adopt?

----

Using an NS collection type as an argument is fine as long as it make sense to do so.  Many Cocoa APIs make use of "NS type" collection arguments for batching like arguments.  But, I would also not call variable argument lists "antiquated", they serve a specific purpose, and definitely should not be equated with arrays (real C arrays or otherwise). Also as a side, as soon as you introduce NS collection types, dynamic memory allocation is happening in one form or another (if not by you, by your caller), it's inherent to the language extension.

----

Using va_list is faster (no dynamic allocations) and somewhat less verbose. If you don't mind the extra typing, and you're not writing time-critical code, you may as well use Cocoa collections instead.

