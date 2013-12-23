---
layout: page
title: WorkingWithSelectors
---

General/NSSelectorFromString is a Foundation Framework function used to convert the name of a method into the actual selector value (almost like a pointer to a function) of type SEL, which you can then use to test if a certain object has that method  which you want to call (using the General/NSObject method respondsToSelector).

**Function Prototype:**

SEL General/NSSelectorFromString(General/NSString *aSelectorName)

----
**Note:** It is possible to statically convert the name of a method into the selector using the compiler directive @selector, for example, **@selector(myMethod:)**. You would use the function General/NSSelectorFromString in situations where you need to dynamically choose a selector.  The @selector construct is compiled, and thus does not have the run time flexibility of General/NSSelectorFromString.
----

The symmetrical function to General/NSSelectorFromString is the Foundation function General/NSStringFromSelector:

**Function Prototpye:**

General/NSString *General/NSStringFromSelector(SEL aSelector)

----

So, what can you do with selectors in your code?  One useful thing i discovered is a quick and easy way to simultaneously change the attributes for a set of controls, i.e, disabling a set of data entry text fields.

Suppose you have 5 text field controls with outlets in your code:

    
// somewhere in the interface file where you declare instance variables...
id General/IBOutlet textField1;
id General/IBOutlet textField2;
id General/IBOutlet textField3;
id General/IBOutlet textField4;
id General/IBOutlet textField5;


Now, suppose you want to disable, or enable all of these text fields in response to some user action.  Then normally you would do the following:

    
[textField1 setEnabled:NO];
[textField2 setEnabled:NO];
[textField3 setEnabled:NO];
[textField4 setEnabled:NO];
[textField5 setEnabled:NO];


Its wouldn't be difficult to imagine how tedious this could become when working with more controls.  So, my solution to this is to have an General/NSArray instance variable, call it textFields, and initialize it in the following way (perhaps in awakeFromNib):

    
// somwhere in awakeFromNib...assuming General/NSArray *textFields is a declared instance variable

textFields = General/[[NSArray alloc] initWithObjects:textField1,
                                              textField2,
                                              textField3,
                                              textField4,
                                              textField5,
                                              nil];


And now we have an array whose members are the five text field outlets.

We can now disable the controls by using the General/NSArray method makeObjectsPerformSelector:withObject:.  Here the withObject: argument would be the object that would go in the argument of the selector; there can be only one.  So, we have:

    

[textFields makeObjectsPerformSelector:@SEL(setEnabled:) withObject:nil];



this has the same effect as what we did line by line above, the tedious way.  setEnabled takes a BOOL as its argument; nil is equivalent to NO.  To re-enable the text fields contained in the array we would do the following:

    
[textFields makeObjectsPerformSelector:@SEL(setEnabled:) withObject:@"YES"];


Somebody correct me if i'm wrong, but the value of the string being YES isn't important here.  What is important is that the argument object is something other than nil, which in the context of a BOOL argument would evaluate to YES.

----

I'm pretty sure the problem with the YES/NO thing is that they're not strings, they're numbers. Use YES and NO instead of @"YES" and nil. C and Obj-C use numbers for bool values. 0 is false and everything else is true. That's why the string @"YES" works, because it's not 0! nil expands to 0, YES and NO expand to 1 and 0 respectively. I think. -- General/PeterMonty

----

I think General/PeterMonty's explanation covers it.  I had a occasion where I had a property that takes a BOOL argument and I tried to set it to NO using General/[NSNumber numberWithBool:NO] as the object but it kept setting the property to YES.  So the 'object' when used with a BOOL appears to just evaluate if the object exists, not the actual value.  Slightly awkward but makes sense.
