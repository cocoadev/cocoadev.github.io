---
layout: page
title: ForallMacro
---

http://sigpipe.macromates.com/2004/09/13/iterating-an-array/

By AllanOdgaard. Relies on C99 mode, but lets you use forall(array, item) { ... }, which I know we all want to do.

Stick it in your precompiled project header and enjoy! -- RobRix (Just updated the link again. As pointed out below, nested uses will work just fine since shadowed variables will automatically unshadow and no one's toes will be stepped on.)

----

When I saw this, I was conflicted between "cool" and "argh!", but I passed it around the office and the consensus was "cool". Thanks!

*You're very welcome! Out of curiosity, may I ask whence the argh? Could it be less argh? (: -- RobRix*

The argh is just a general fear/cautiousness thing with C macros, particularly C macros that pretend to be real syntax elements, since something invariably goes wrong in ugly ways with them. But it looks like this particular one doesn't fall victim to that, so good job!

By the way, I would suggest adding parentheses around the assignment in the for loop, as it shuts up warnings if they're turned up high enough to try to catch =/== confusion.

----

Another cause of arghitude is that when you use macros like this, you're sort of writing in your own extension the C language, which means that your code is non-standard, more complex, and somewhat less readable (though it's somewhat balanced out in this case by the readability advantages of forall over for).  The basic idea of a forall macro is pretty clear, but different implementations are liable to be different in possibly important ways.  The solution is real standardization, i.e. making the construct a part of the language.

----

I'm of the school of thought that you're always in the process of building the language you need to solve your problem, using the specified language as simply a starting point. Given that, extensions to the language aren't automatically bad, and even start out with some good points.

I'm curious as to how you consider that macros like this automatically make the code less readable. The way I see it, this depends on the macro: something weird that hides way too much makes things less readable, but a straightforward syntax addition makes things more readable. Everything we use hides the implementation details to some extent, the question is whether it hides them in such a way as to expose you to them in hidden ways or not. Unfortunately, C macros almost always expose their implementation details in difficult-to-diagnose ways, but this one seems to be a rare exception.

While I agree that in theory the solution is standardization, waiting a decade or two for such a proposal to make its way through committee (or more likely, get shot down) is not a good way to get work done.

----

*I'm curious as to how you consider that macros like this automatically make the code less readable. The way I see it, this depends on the macro*

Well, that's kind of what I mean.  People who write in lisp talk about extending the language, but that's totally different from the way a macro extends C.  In lisp the (lack of) syntax makes it obvious what can affect what, and the language extensions are encapsulated.  C macros can run amuck - you really cannot afford to treat a macro as opaque.  So it isn't like I'm necessarily against using 'em, but I do think that they increase the complexity of code.

----

My take on the matter:

    

/*
 foreach(each, aCollection) {
     [each doSomething];
 }
 */
#define foreach(element, collection) for(id element, foreach_ ## element ## _enumerator = [collection objectEnumerator]; element = [foreach_ ## element ## _enumerator nextObject];)


/*
 float floats[10];
 float sum = 0;

 cforeach(float, element, floats, sizeof(floats)) {
     sum += *element;
 }
*/
#define cforeach(type, element, array, count) for(type *element, *foreach_current_ ## element ## _pointer = array, *foreach_end_ ## element ## _pointer = array + count; ((element = foreach_current_ ## element ## _pointer) || 1) && (foreach_current_ ## element ## _pointer != foreach_end_ ## element ## _pointer); foreach_current_ ## element ## _pointer++)



/Mr. Fisk (Fixed some typos and made the element in cforeach() a pointer)

----

Does anybody have a genius idea as to how to allow static typing in the     forall statement? I'd like to be able to write things like this:
    
forall(NSFoo *foo, fooArray) { ... }

Using the declaration from the     element is fairly easy, but it messes up the symbol munging used for the internal enumerator variable. Is there a clever way to make this work?

----

My best shot: forall(NSFoo, foo, fooArray) { ... }

    

#define forall(type, element, collection) for(type *element, *foreach_ ## element ## _enumerator = (id)[collection objectEnumerator]; element = [(id)foreach_ ## element ## _enumerator nextObject];)



/Mr. Fisk

*Suggestion: try using __LINE__ to get a unique enumerator variable.*


----

It helps, but I still can't get past the problem in the     element = [enumerator nextObject] part. If you let element be     NSObject *element it expands to     NSString *element = [enumerator nextObject], which won't even compile.

----

Alright, I came up with a workable solution for the     foreach (NSFoo *foo, fooArray) { ... } form:

    

#define foreach(element, collection) for(id foreach_##collection##_element, foreach_##collection##_element_enumerator = [collection objectEnumerator]; foreach_##collection##_element = [foreach_##collection##_element_enumerator nextObject];) \
for (element = foreach_##collection##_element; foreach_##collection##_element; foreach_##collection##_element = nil)



// Mr. Fisk

Wow, that is super evil, but it looks like it gets the job done.

On second thought, it actually doesn't. It breaks the semantics of the     break keyword, since it will only exit the inner for loop and not the outer one. Still closer than before, though...

----

    

#define foreach(element, collection) for(id foreach_##collection##_element, foreach_##collection##_element_enumerator = [collection objectEnumerator]; foreach_##collection##_element = !foreach_##collection##_element ? [foreach_##collection##_element_enumerator nextObject] : nil;) \
for (element = foreach_##collection##_element; foreach_##collection##_element; foreach_##collection##_element = nil)



That should work for both break and continue.

*Looks good. One minor problem:     foreach_##collection##_element needs to be initialized to nil in the declaration. As it is, it's uninitialized, and if it happens to get a non-nil value, then the loop will terminate immediately. Otherwise, I see no problems.*


----

On second thought, I do see a problem.     foreach(NSString *string, [dictionary allKeys]) would not work for example. I really do like the intention of the macro, if only there was a way to generate unique variable names without using any of the macro's arguments all would be well.

/Mr. Fisk

*Do you really need unique variable names, though? As long as you make up a variable name that's never used outside of the forall-generated code, it doesn't matter if nested forall statements shadow each other's variables, because they will be unshadowed when actually used.*

----

*You do have a point. The following seems to do everything we'd want it to. I've tried nesting a few foreach-loops, with them all accessing the same array and various stuff, but I did not manage to break it.*

    

#define foreach(element, collection) for(id foreach_macro_element = nil, foreach_macro_enumerator = [collection objectEnumerator]; foreach_macro_element = !foreach_macro_element ? [foreach_macro_enumerator nextObject] : nil;) \
for (element = foreach_macro_element; foreach_macro_element; foreach_macro_element = nil)



*Still evil, but it looks like you got it! :-) Nice work.*

----

Infact, it doesn't always work, since the c preprocessor doesn't understand objective-c's message syntax.

The following code will fail, since the preprocess thinks that the arguments passed to +[NSArray arrayWithObjects:] should be passed to the macro.

    

foreach (NSString *string, [NSArray arrayWithObjects:@"string1", @"string2", nil]) {
    NSLog(string);
}



And the lesson is still that macros are evil, even I can admit that now :)

/Stefan Fisk

----

Of course, lesson should never be learnt :)

This SEEMS to solve the problem:

    

#define foreach(element, ...) \
for(id foreach_macro_element = nil, foreach_macro_enumerator = [__VA_ARGS__ objectEnumerator] \
    ; \
    foreach_macro_element = !foreach_macro_element ? [foreach_macro_enumerator nextObject] : nil \
    ; \
    ) \
    for (element = foreach_macro_element \
         ; \
         foreach_macro_element \
         ; \
         foreach_macro_element = nil)



----

I was inspired by this  GCC bug report: http://gcc.gnu.org/bugzilla/show_bug.cgi?id=16903

    

#define CONCAT_(a, b) a ## b

#define CONCAT(a, b) CONCAT_(a, b)

#define FOREACH(item, array) NSEnumerator *CONCAT(__u_enum, __LINE__) = [array objectEnumerator]; while ((item = [CONCAT(__u_enum, __LINE__) nextObject]))



--Iain

----
Unfortunately, by having multiple statements in the macro, it stops acting like a normal control construct. For example, this will fail:

    
FOREACH(str, strings)
    FOREACH(item, items)
    {
        ...
    }


Whereas if you used     for or     while instead, that would work.

----

Here's my implementation. It solves all the problems identified previously, as well as intrinsically allowing you to specify any enumerator accessor on an object. It is also a little more verbose than other implementations *by design*--kind of like method dispatches are more verbose than function calls. It requires you compile as C99.

    
static __attribute__((__always_inline__, __unused__)) NSEnumerator *gt_getEnumerator(SEL selector, id object) {
	return objc_msgSend(object, selector);
}

#define each(KEY) (id gt_flag = (id)1, gt_obj = nil, gt_e = gt_getEnumerator(@selector(KEY##Enumerator),
	 
#define within(OBJECT) (OBJECT)); gt_flag && (gt_obj = [gt_e nextObject]); )

#define as(LVALUE) for (LVALUE = gt_obj; gt_obj && !(gt_flag = (id)0); gt_obj = nil, gt_flag = (id)1)

/* use skipnext to skip the next object in the enumerator */
/* continue and break also work as expected */
#define skipnext (void)[gt_e nextObject]

/* compatibility macro for existing implementations */
#define FOREACH(OBJECT, ...) for each (object) within (__VA_ARGS__) as (OBJECT)


You use it as follows (note that embedding and static typing are both available):

    
/* Yes, I keep changing this. I don't like the examples I keep coming up with! */
for each (object) within ([NSApp windows]) as (NSWindow *window) {
	NSLog(@"path components for %@'s file:", window);
	NSArray *components = window representedFilename] pathComponents];
	for each (reverseObject) within (components) as ([[NSString *component)
		NSLog(@"\t%@", component);
}


----

I'm still (after three or more years!) amazed at the many different efforts people have made at this. Well done! That said, I'm not entirely sure what deficiencies of the very simple original are being addressed by these, and so I'd like to see some discussion of that. What do you think? -- RobRix
----
This seems so absolutely pointless to me. (Allan's original idea was a nice shorthand though)  -- FjolnirAsgeirsson

