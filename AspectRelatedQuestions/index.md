---
layout: page
title: AspectRelatedQuestions
---

**AspectRelatedQuestions** - Burning questions about the Objective-C runtime and other snags all related to helping me fully implement AspectCocoa

* How does class posing actually work, and why is it that if I pose as a class that already has some instances instantiated, those instances don't get changed to the type of the new posing class?  

That would be really hard to do.  If your subclass added instance variables, or was a completely different kind of class, someone would have to find all the allocated objects of that class, and somehow change them into your class, which is a hard problem and not worth it for the few times poseAs is handy.

* While all of: AddClassAspects, PoseAsAspects, and AddMethodAspects work to wrap some class defined by the user, none of them work to wrap NSDocument, NSMutableArray or NSObject (and presumably the rest of foundation/Application). Why?

I have figured out that much of the method wrapping I was trying to do failed, because the methods I was wrapping returned structs of some sort and I was replacing them with methods that returned void*, I have since changed my approach to check the method and replace it with either a void* method or a struct returning method depending, but there are still problems with this.  In that, I need to anticipate every possible struct. Luckily all this means is adding those stucts to a single Union.  But, for some reason adding void* to that union and then using it to replace both types of methods does not work..  Although I can use this union for the Argument types of a method. (which would work when I have previously only used void* but would mangle the members of structs such as NSSize)  It comes down to this... a pure C question, how to write a method that takes any number of arguments that could be of ANY type and return something that could be of ANY type.  (the any number of arguments question isn't as important, as finding a C type that could be used to replace ANYTHING) perhaps such a type does not yet exist.  I wonder, how hard would it be to add such a thing to gcc?

*That would be pretty hard, since C doesn't have caller metadata.  The caller just shoves arguments on the stack, and the receiver just knows how to pull the arguments off, either via explicitly named paramters (so the compiler can take paramters and stick them in variables, or just have the variable names reference the memory on the stack), or through varargs. (man stdarg)  But when using varargs, the receiver either has to have a description of what's on the stack (like through printf's format string), or pulls off arguments of an expected size (like pulling off object pointers until nil is found)*

How does the function result then get returned to the sender?  same way, on the stack? (it would seem from the behavior I've encountered, that return typing is a more strict mechanism)

*Yeah, through the stack.  It could also be returned in a register, depending on the compiler's implementation of stuff.  It could also be returned into an area set aside by the caller - like when returning an NSRect, if it's going into a variable, the values could be deposited directly into the variable's space, otherwise it could be put into a sizeof(NSRect) space on the stack*

what we really want to do is just not pull the arguments off the stack, and pass them on to some function call we will make. Is there a way to directly manipulate the stack in this manner?

So then, how about this scenario...  The caller puts arguments on the stack and the reciever just doesn't pull them off.. (except for the first 2 arguments which are consistently of type SEL and id...)  then the next function called can pull off the arguments put on by the previous function?  So we'd therefor need to call a function with say 5 arguments but only give it 2 and have it pull the rest off the stack...  Anything like this possible?

*that'd be hard to do, since you'd need to skip over the local variables and other stack-frame stuffage in the final method*

I've discovered that I can replace any objc method IMP with n or fewer arguments with an IMP that was compiled as taking exactly n arguments.(where n>1) And it works as far as I can tell just as if I had replaced each IMP with an IMP that was designed to take the exact same number of args as it's replacer.  What do you think is happening underneath to make this possible?  is the reciever pulling garbage off the arguments stack after it gets it's first few args?

*Yep*

**NSLog is an example of a method that does what I need my methods to do, it takes a variable number of arguments and they can be of any type... ids.. floats.. ints.. etc... So, how does NSLog do it's magic? I can use NSMethodSignature to find out what the args are.. I just need a method that can handle em' like that**

*NSLog scans the string it's given for formatting arguments. So if you give it incorrect formatting directions, your app dies--there's no magic.*

you're so negative... I looked at some GNUStep source and man starg as you suggested... I can use NSMethodSignature to find out what the types and lengths of the args would be.. this almost works.  It works as a standalone method, but when I make a pointer to the method and stick it into the IMP of a method that has a set number of args with set types.. the conversion is incorrect. (i.e. a float that should be 2.2 becomes 1.0)...  So it seems I can't use these interchangably.. (functions that have fixed num arguments and functions that have var num arguments)  The magic in NSLog I was talking about is basically the va_list functions.  Still, it's not working... 

Can you tell me more about Unions? Can they be used to combine types.. etc?

Also, I can use void * to make a pointer to anything right? but if I want to dereference that pointer back to it's original value without knowing it's type... any chance?

are all pointer types interchangeable? (of the same length)? what about pointers to a pointer?  could I make a variable that's a pointer to a pointer to something that could be of any type...?

*How can I write a generic objective-c "replacement" method that can accept arguments of any type and return something of any type (struct, id and others...)? 

*How does NSMethodSignature get the return types and arg type information about a method.  I do not see this information available via any runtime data type of function. (and according to the above post, C doesn't even keep this information.. so where does it come from?)

