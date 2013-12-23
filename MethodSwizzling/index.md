---
layout: page
title: MethodSwizzling
---



The Objective-C runtime lets you modify the mappings from a selector (method name) to an implementation (the method code itself).  This allows you to "patch" methods in code you don't have the source to (AppKit, FoundationKit, etc).  Unlike creating a category method with the same name as the original method (effectively replacing the original method), MethodSwizzling lets your replacement method make use of the original method, almost like subclassing.

This is best used in cases where a single method needs substitution or extension;  If you need to modify many behaviours of a class, you may be better off using ClassPosing.

December 29, 2007:
[http://mjtsai.com/blog/2007/12/29/jrswizzle/]  Jonathan Rentzsch has started a project, JRSwizzle  [http://github.com/rentzsch/jrswizzle], to implement method swizzling correctly with different versions of the Objective-C runtime:

There's at least four swizzling implementations floating around. Here's a comparison chart to help you make sense of how they relate to each other and why JRSwizzle exists.

----**An illustration**----

For instance, let's pretend there's a class called Foo that implements the following method:

    
 // returns the login name of the current user
 - (NSString *)fooBar;


Now we've got applications that call Foo sharedFoo] fooBar] all over the place;  We'd like to modify the functionality of the fooBar method to append something silly to the result of the original fooBar return value.

So, we'll implement a category method that does the work:

    
 @implementation Foo(specialfooBar)
 // returns the login name of the current user plus some other junk
 - (NSString *)myfooBar
 {
     return [[self myfooBar] stringByAppendingString:@", bigtime luser"];
 }
 @end


"But wait", I hear you saying, "the myfooBar method is calling itself, generating infinite recursion!"  Well, that would be the case except for the fact that we're going to do some [[MethodSwizzling and swap the implementations of the two methods!  Here's the deal:  Right now, any code that calls -[Foo fooBar] is going to use the original implementation (let's call that "A"), and any code that calls -[Foo myfooBar] is going to use our new implementation (let's call that "B").  To summarize:

    
 selector "fooBar"    ->  implementation "A"
 selector "myfooBar"  ->  implementation "B"
 
 - implementation "A" returns @"username"
 - implementation "B" calls "myfooBar",
  appends some silly text, and returns that


So right now, calling "fooBar" invokes implementation "A" like it always has;  Calling "myfooBar", however, invokes implementation "B", which in turn calls "myfooBar", which invokes implementation "B", etc, leading to the unwanted infinite recursion.

    
 // Do the swizzling (see below for the function code)
 MethodSwizzle([Foo class],
               @selector(fooBar)
               @selector(myfooBar));

After swizzling the two methods, we have the following situation:

    
 selector "fooBar"    ->  implementation "B"
 selector "myfooBar"  ->  implementation "A"
 
 - implementation "A" returns @"username"
 - implementation "B" calls "myfooBar",
   appends some silly text, and returns that


Now, after the swizzling, anybody who calls "fooBar" will invoke implementation "B", which calls "myfooBar" (invoking implementation "A" and appending some silly text).  In short, calling "fooBar" will now return @"username, bigtime luser"!

Hopefully this makes sense to people!

--JackNutting

----**The MethodSwizzle function**----

Here's a C-function that does the trick:

    
 #import </usr/include/objc/objc-class.h>
 void MethodSwizzle(Class aClass, SEL orig_sel, SEL alt_sel)
 {
     Method orig_method = nil, alt_method = nil;
 
     // First, look for the methods
     orig_method = class_getInstanceMethod(aClass, orig_sel);
     alt_method = class_getInstanceMethod(aClass, alt_sel);
 
     // If both are found, swizzle them
     if ((orig_method != nil) && (alt_method != nil))
     {
         char *temp1;
         IMP temp2;
 
         temp1 = orig_method->method_types;
         orig_method->method_types = alt_method->method_types;
         alt_method->method_types = temp1;
 
         temp2 = orig_method->method_imp;
         orig_method->method_imp = alt_method->method_imp;
         alt_method->method_imp = temp2;
     }
 }


----

Here's code that uses it:
    
 @interface T : NSObject { }
 - (void)swizzleMethod;
 @end
 @interface T (AltMethod)
 - (void)altMethod;
 @end
 
 int main (int argc, const char * argv[])
 {
     T *t;
     t = T alloc] init];
     [t swizzleMethod];
 
     NSLog(@"Methods swizzled");
     MethodSwizzle([T class],
                   @selector(swizzleMethod),
                   @selector(altMethod));
     
     [t swizzleMethod];
     [t release];
     
     return 0;
 }
 
 @implementation T
 - (void)swizzleMethod
 {
     NSLog(@"Original method called");
 }
 @end
 
 @implementation T (AltMethod)
 - (void)altMethod
 {
     NSLog(@"Alternative method called");
     [self altMethod];
 }
 @end

Note the use of a category, just to check everything works correctly.

Here's the output as logged:
    
 2002-04-09 17:23:58.144 Swizzling[2285] Original method called
 2002-04-09 17:23:58.148 Swizzling[2285] Methods swizzled
 2002-04-09 17:23:58.150 Swizzling[2285] Alternative method called
 2002-04-09 17:23:58.152 Swizzling[2285] Original method called


----

Hurrah! It works! I have also checked it by swizzling the init method of [[NSObject (successfully).

-- KritTer

----
Indeed it does... but I wonder what difference it makes that you not only swap the IMP pointers in your implementation, but also the method_types... it seems to not make a difference.  Anybody know of a case where this matters?

-- IgotiMac & AspectCocoa

----

Hmmm, this works partly. I also tried to swizzle the init method of NSObject. Somehow the altInit I provided only gets called when an instance of NSObject is created. It definitely is NOT called for things like myString = [NSString stringWithString:@"test"];

This got me puzzled. I'm using GCC v3.3 and didn't have a chance yet to try with older versions. If anyone else has experience using these features I'd be interested in any information on this subject.

-- Jan Willem Luiten

----
Jan: A couple of points that might help:

First:  The message     [NSString stringWithString:@"test"] is equivalent to     [NSString alloc] initWithString: @"test"] autorelease].  So to understand what's going on you need to understand how the +alloc and -init messages are implemented.

Second:  [[NSString is a class cluster, which means that its +alloc method does not return an instance of NSString.  It returns instead a (singleton) instance of a (private) subclass of NSString.  (I think it's the NSPlaceholderString class, but the exact name doesn't matter so let's just suppose that name is correct.)  Thus the -initWithString: message is sent to the (singleton) instance of this (private) subclass and NOT to an instance of the NSString class.

Third:  The message     [NSPlaceholderString initWithString: @"test] returns an instance of a concrete (private) subclasses of NSString, where the appropriate concrete subclass is determined by the form of the initialization message -- eg, -initWithString, initWithCString, initWithFormat, etc.

Fourth:  Given the above, the message     [NSString stringWithString:@"test"] does not invoke NSObject's implementation of the -init method.

----
Except that normally every class's init methods start out by calling the superclass's init methods.  However, you're right in this case.  For whatever reason, setting a breakpoint shows that     [NSString stringWithSting:@"test"] doesn't call through to     -[NSObject init].  Maybe it's because of the bridging to CFString - a core foundation init method may be used instead.

----
Question:  Why would Apple adopt use this sort of design? 

Answer:  They want to be able to optimize string objects by having one implementation of NSString's interface tuned specifically for C strings, another tuned specifically for Objective-C strings, etc in a way that encapsulates their implementation decisions.  Proof of the effectiveness of this design is that you've been using the nice, clean, elegant interface of the public class NSString without any idea that you were working with instances of multiple private subclasses!  Yet Another Proof of the great design of Cocoa frameworks ;-)

jd

----

Works great, but for my project, I modified MethodSwizzle() to return an error code if one of the methods can't be found.

----

For a useful example of MethodSwizzling check out InstanceCounting. --TheoHultberg

----

I suggest the following, to more closely match poseAsClass:'s interface...

    
 @implementation NSObject(Swizzle)
 
 + (void)swizzleMethod:(SEL)orig_sel withMethod:(SEL)alt_sel {
     Method orig_method = nil, alt_method = nil;
 	
     // First, look for the methods
     orig_method = class_getInstanceMethod([self class], orig_sel);
     alt_method = class_getInstanceMethod([self class], alt_sel);
 	
     // If both are found, swizzle them
     if ((orig_method != nil) && (alt_method != nil)) {
         char *temp1;
         IMP temp2;
 		
         temp1 = orig_method->method_types;
         orig_method->method_types = alt_method->method_types;
         alt_method->method_types = temp1;
 		
         temp2 = orig_method->method_imp;
         orig_method->method_imp = alt_method->method_imp;
         alt_method->method_imp = temp2;
 	} else NSLog(@"Attempt to swizzle nonexistent methods!");
 }
 
 @end

Edit: Sorry, I made a bad mistake in the code last time that could actually cause a possible crash instead of an error message... that's what happens when you don't test code before you post it, I guess. What horrible form. ;-)

--RobinHP

----

Is it really necessary to copy over the "method_types"?  They should be same to the extent that the IMP your replacing should take the same arguments as the orginal, right?  Otherwise, would there not be a problem with missing or extra arguments?  Maybe, they would just be ignored or "nil/NULL" inserted as necessary?

Others such code examples, specifically that in Anguish et al. in "Cocoa Programming" at page 1083 and those archived at CocoaBuilder, mention the need to call "_objc_flush_caches_(Class)" after swizzling to keep the runtime kosher.  I have tried this and found that I still get some runtime strangeness, like specific confusion about inheritence and the states of ivars.  After reading this page, which does not mention the use of this function, I ran my software without it and my ivar state problem appeared to go away.  Does anybody know what the story with "_objc_flush_caches_(Class)" and method swizzling really is?

- DumberThanDanQuayle

----

This seems to have one *huge* disadvantage: If a class inherits its method from its superclass, this code will replace the method in the superclass. So, if you swizzle a method in NSButton that it actually inherits from NSView, all other NSViews will also have that method. So, you can't willy-nilly swizzle methods in several subclasses of the same class unless you are sure they all implement their own version. Moreover, if you do it with an Apple class and Apple moves the implementation into the superclass, you're screwed.

-- UliKusterer

----
Personally, I find this whole thing rather hackish and have avoided it altogether. I am fully prepared to admit this may be ignorance or fear of the unknown talking, but it just seems wrong. ;-)

----
That's a very important point, Uli.  We should work on this code and see what can be done.  What comes to mind for me is that what we *really* want is dynamic subclassing + posing or isa-swizzling.  FScript has this, though sort of in beta, and (I think?) PyObjC has it, so we just need to get an ObjC implementatation going.  

----

Alternatively, one could do the following:

    
 @implementation T (AltMethod)
 - (void)altMethod
 {
     if ([self isKindOfClass:[ExpectedClass class]]) {
         NSLog(@"Alternative method called");
     } else { // Call the method we swizzled out
         [ExpectedClass methodForSelector:@selector(altMethod)](self,@selector(origMethod));
     }
 }
 @end


I guess the above should be corrected as follows:

    
...
        [ExpectedClass instanceMethodForSelector:@selector(altMethod)](self,@selector(origMethod));
...


----

If a class inherits its method from its superclass, add a stub method with the same selector that just calls the superclass' method.  Now you can swizzle it like any other method and not have to worry about unintended side effects to other classes.

Hackish?  Oh yeah.

-- Brooke C.

----
If you want to do this sort of thing with dynamically created classes take a look at my code at WeakPointers. The aforementioned code worked well so long as I didn't try it on any CoreFoundation classes.

-jason

----

I just wrote a new implementation of Method Swizzling that fixes the inherited methods problem. You can read about it here:

http://kevin.sb.org/articles/2006/12/30/method-swizzling-reimplemented

-Kevin Ballard

----

I updated Kevin's implementation to use the 10.5-style objc runtime API. 
- Aaron Harnly

Pasted here:
    
 // if the origSel isn't present in the class, pull it up from where it exists
 // then do the swizzle
 BOOL _MyPluginTemplate_PerformSwizzle(Class klass, SEL origSel, SEL altSel, BOOL forInstance) {
     // First, make sure the class isn't nil
 	if (klass != nil) {
 		Method origMethod = NULL, altMethod = NULL;
 		
 		// Next, look for the methods
 		Class iterKlass = (forInstance ? klass : klass->isa);
 		unsigned int methodCount = 0;
 		Method *mlist = class_copyMethodList(iterKlass, &methodCount);
 		if (mlist != NULL) {
 			int i;
 			for (i = 0; i < methodCount; ++i) {
 				
 				if (method_getName(mlist[i]) == origSel) {
 					origMethod = mlist[i];
 					break;
 				}
 				if (method_getName(mlist[i]) == altSel) {
 					altMethod = mlist[i];
 					break;
 				}
 			}
 		}
 		
 		if (origMethod == NULL || altMethod == NULL) {
 			// one or both methods are not in the immediate class
 			// try searching the entire hierarchy
 			// remember, iterKlass is the class we care about - klass || klass->isa
 			// class_getInstanceMethod on a metaclass is the same as class_getClassMethod on the real class
 			BOOL pullOrig = NO, pullAlt = NO;
 			if (origMethod == NULL) {
 				origMethod = class_getInstanceMethod(iterKlass, origSel);
 				pullOrig = YES;
 			}
 			if (altMethod == NULL) {
 				altMethod = class_getInstanceMethod(iterKlass, altSel);
 				pullAlt = YES;
 			}
 			
 			// die now if one of the methods doesn't exist anywhere in the hierarchy
 			// this way we won't make any changes to the class if we can't finish
 			if (origMethod == NULL || altMethod == NULL) {
 				return NO;
 			}
 			
 			// we can safely assume one of the two methods, at least, will be pulled
 			// pull them up
 			size_t listSize = sizeof(Method);
 			if (pullOrig && pullAlt) listSize += sizeof(Method); // need 2 methods
 			if (pullOrig) {
 				class_addMethod(iterKlass, method_getName(origMethod), method_getImplementation(origMethod), method_getTypeEncoding(origMethod));
 			}
 			if (pullAlt) {
 				class_addMethod(iterKlass, method_getName(altMethod), method_getImplementation(altMethod), method_getTypeEncoding(altMethod));
 			}
 		}
 		
 		// now swizzle
 		method_exchangeImplementations(origMethod, altMethod);
 		
 		return YES;
 	}
 	return NO;
 }


and here it's used to implement an NSObject category adding swizzling methods to all classes:
    
 @interface NSObject (AWHSwizzle)
 + (void)swizzleMethod:(SEL)orig_sel withMethod:(SEL)alt_sel;
 + (void)swizzleClassMethod:(SEL)orig_sel withClassMethod:(SEL)alt_sel;
 @end
 
 @implementation NSObject (AWHSwizzle)
 + (void)swizzleMethod:(SEL)orig_sel withMethod:(SEL)alt_sel {
     NSString *originalMethodName = [NSString stringWithCString:sel_getName(orig_sel)];
     NSString *alternateMethodName = [NSString stringWithCString:sel_getName(alt_sel)];    
     NSLog(@"Attempting to swizzle in class '%@': swapping method '%@' with '%@'...",[self class], originalMethodName, alternateMethodName);
 	
 	_MyPluginTemplate_PerformSwizzle([self class], orig_sel, alt_sel, YES);
 }
 
 + (void)swizzleClassMethod:(SEL)orig_sel withClassMethod:(SEL)alt_sel {
     NSString *originalMethodName = [NSString stringWithCString:sel_getName(orig_sel)];
     NSString *alternateMethodName = [NSString stringWithCString:sel_getName(alt_sel)];    
     NSLog(@"Attempting to swizzle in class '%@': swapping class method '%@' with '%@'...",[self class], originalMethodName, alternateMethodName);
 	_MyPluginTemplate_PerformSwizzle([self class], orig_sel, alt_sel, NO);
 }
 @end
 



----

The above code (Aaron's) had some problems, that, for me, resulted in a crash (maybe it works if you have GC or in some configuration; the problem was that at least one of the Method object used in the call to method_exchangeImplementations potentially points to a Method that is not the method created with class_addMethod). Hopefully, this will work - CharlesParnot

Paste this in replacement of original Kevin's code for the function _PerformSwizzle:
    
 BOOL _PerformSwizzle(Class klass, SEL origSel, SEL altSel, BOOL forInstance) {
   
   // Make sure the class isn't nil
   if (klass == nil)
     return NO;
   
   // Look for the methods in the implementation of the immediate class
   Class iterKlass = (forInstance ? klass : klass->isa);
   Method origMethod = NULL, altMethod = NULL;
   NSUInteger methodCount = 0;
   Method *mlist = class_copyMethodList(iterKlass, &methodCount);
   if ( mlist != NULL ) {
     int i;
     for (i = 0; i < methodCount; ++i) {
       if ( method_getName(mlist[i]) == origSel )
         origMethod = mlist[i];
       if (method_getName(mlist[i]) == altSel)
         altMethod = mlist[i];
     }
   }
   
   // if origMethod was not found, that means it is not in the immediate class
   // try searching the entire class hierarchy with class_getInstanceMethod
   // if not found or not added, bail out
   if ( origMethod == NULL ) {
     origMethod = class_getInstanceMethod(iterKlass, origSel);
     if ( origMethod == NULL )
       return NO;
     if ( class_addMethod(iterKlass, method_getName(origMethod), method_getImplementation(origMethod), method_getTypeEncoding(origMethod)) == NO )
       return NO;
   }
   
   // same thing with altMethod
   if ( altMethod == NULL ) {
     altMethod = class_getInstanceMethod(iterKlass, altSel);
     if ( altMethod == NULL )
       return NO;
     if ( class_addMethod(iterKlass, method_getName(altMethod), method_getImplementation(altMethod), method_getTypeEncoding(altMethod)) == NO )
       return NO;
   }
   
   //clean up
   free(mlist);
   
   // we now have to look up again for the methods in case they were not in the class implementation,
   //but in one of the superclasses. In the latter, that means we added the method to the class,
   //but the Leopard APIs is only 'class_addMethod', in which case we need to have the pointer
   //to the Method objects actually stored in the Class structure (in the Tiger implementation,
   //a new mlist was explicitely created with the added methods and directly added to the class;
   //thus we were able to add a new Method AND get the pointer to it)
   
   // for simplicity, just use the same code as in the first step
   origMethod = NULL;
   altMethod = NULL;
   methodCount = 0;
   mlist = class_copyMethodList(iterKlass, &methodCount);
   if ( mlist != NULL ) {
     int i;
     for (i = 0; i < methodCount; ++i) {
       if ( method_getName(mlist[i]) == origSel )
         origMethod = mlist[i];
       if (method_getName(mlist[i]) == altSel)
         altMethod = mlist[i];
     }
   }
   
   // bail if one of the methods doesn't exist anywhere
   // with all we did, this should not happen, though
   if (origMethod == NULL || altMethod == NULL)
     return NO;
   
   // now swizzle
   method_exchangeImplementations(origMethod, altMethod);
   
   //clean up
   free(mlist);
   
   return YES;
 }


Note that the above code should probably use a plain old "unsigned int" instead of "NSUInteger" to match the signature of class_copyMethodList().

----

Make sure your calls to swizzleMethod only happen once. I made the mistake of putting the swizzle procedure in the '+initialize' method of a class in a category, and my swizzle procedure ended up running twice, effectively un-swizzling what had ben swizzled.

- JaeKwon

----

In researching this, it seems to me that the existing solutions for the Leopard runtime are extremely over-engineered. There are two cases to consider: the subclass implements the method you're replacing, or it inherits it. In the former case,     method_exchangeImplementations gets the job done. In the latter case,     class_addMethod on the "old" selector with the new method, followed by     class_replaceMethod on the "new" selector with the old method, will get things into a good place. Since     class_addMethod returns success or failure based on whether the class in question already has such a method, this results in this easy code:

    
 void Swizzle(Class c, SEL orig, SEL new)
 {
     Method origMethod = class_getInstanceMethod(c, orig);
     Method newMethod = class_getInstanceMethod(c, new);
     if(class_addMethod(c, orig, method_getImplementation(newMethod), method_getTypeEncoding(newMethod)))
         class_replaceMethod(c, new, method_getImplementation(origMethod), method_getTypeEncoding(origMethod));
     else
         method_exchangeImplementations(origMethod, newMethod);
 }


Seems foolproof, and of course is extremely simple compared to the other functions offered here. Have I missed something, or shall we remove those and leave this one as the example of the right way to do this? -- MikeAsh

----
Corrected omitted code in MikeAsh's code above. It does seem to work, at least with a quick test. Steve Weller.

----
If the subclass (B) inherits the old method from its superclass (A), won't pulling it down result in the method being called twice, if I call the old method in my new method?

E.g. when swizzling release with myRelease, I would write somthing like:

    
 - (void) myRelease {
     NSLog(@"Extremely important log message");
     [self myRelease];
 }


This calls the "old" release method from B, which was pulled down from superclass A. This method most likely contains a "[super release]", which then calls the original release method from the superclass A. Or am I missing something?

----
Sorry, I am quite a beginner with objective c and cocoa dev. Does method swizzling affect all programs / apps that are executed or only the program that does the swizzling?
-Hans

----
@Hans. It can affect other programs being executed if you are swizzling methods from Apple's libraries. In my experience, it has especially affected jailbroken devices.
-Allan

