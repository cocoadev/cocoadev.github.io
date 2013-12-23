---
layout: page
title: AccessorMethods
---



There is a good article on accessor methods at http://www.stepwise.com/Articles/Technical/2002-06-11.01.html

It boils down to a few basic patterns for accessors/setters depending upon what you want to do, how precise you can be about what threads things will be called from, and a few other details.

**If careful with output of accessor and only using a single thread**     
- (id)var
{
    return _var;
}


**Basic setter**     
- (void)setVar:(id)newValue
{
    if (newValue != _var) {
        [_var release];
        _var = [newValue retain];
    }
}

If it is the value passed in that you want, not the actual object storing it, you should consider doing a copy instead of a retain at (*).

----


In the overwhelming majority of cases, making your accessors individually ThreadSafe is a crazy level of granularity: not only are you killing performance, but trying to code anything sensible without having a higher level of locking (and hence negating the point of ThreadSafe accessors anyway) will be incredibly hard. You are almost certainly better off removing the simple accessors and writing larger blocks of code, perhaps protected by the new SynchronizedDirective.

If you are *sure* want your accessors to be individually ThreadSafe in the face of multiple threads accessing and setting indiscriminantly, none of the above code will do, and the following is the standard alternative:

**Multithreaded setter**     
- (void)setVar:(id)newValue
{
    id originalValue;

    [newValue retain];

    [_varLock lock];
    originalValue = _var;
    _var = newValue;
    [_varLock unlock];

    [originalValue release];
}

This one needs an NSLock in the class, but stops setters colliding. Locks are very expensive, though, so ideally this one should be avoided if at all possible, perhaps by designing your code to guarantee single-thread access to an object. It needs its own paired getter function, too:     
-(id)var 
{
   id tmp = nil;
   [_varLock lock];
   tmp = [_var retain];
   [_varLock unlock];

    return [tmp autorelease];
}


Alternatively, one could use a     @synchronized block here, and reduce the intellectual overhead of maintaining the lock. See LockingAPIs for a discussion of some of the available locking strategies and their various advantages and disadvantages.

----

If you use the basic setter and you are bored of writing accessors over and over then I can strongly recommend Objective-C Services.

it will generate accessors for you from your inst var declarations 

Accessorizer will do the same thing. 

... as does AndrePang's objc-make-accessors Perl script (which is integrated with Xcode's Scripts menu): 

----

On the subject: Why , a must read. It is written in a Java context, but it applies just as much to Objective-C.

A short summary: many getter and setter methods violate encapsulation, which is a bad thing. Do not create getters and setters unless they do something useful.



Allen's description of the evils of get/set are not applicable to ObjC. In Java, setX() means "set the instance variable X." In Objective C it may mean this or it may not. It can be abstracted all you like under the covers. It may have a backing instance variable; it may not. All his concerns over your later changing the type of X are exactly why the ObjC way does not suffer the problem. Creating accessors does *not* expose internal implementation details in ObjC the way it does in Java. You should always create and use getter and setter methods in ObjC in order to *protect* encapsulation. The one worthwhile take-away from Allen's article is that you should make your accessors private (declare them inside the .m) whenever they are not needed by external objects. Don't expose your accessors just because you made them. --RobNapier

----

When writing a setter for a mutable collection class (eg NSMutableArray), is it evil to do this:

    

-(void)setMyArray:(NSMutableArray *)newArray
{
    if (myArray != newArray)
    {
        [myArray setArray:newArray];
    }
}


instead of


    
-(void)setMyArray:(NSMutableArray *)newArray
{
    if (myArray != newArray)
    {
        [myArray release]
        myArray = [newArray mutableCopy];
    }
}

?

*Evil or not, in the first case you will end up with a reference to an array that can be changed from somewhere else, and, most likely you will have a whole bunch of strange bugs because you were sloppy (aren't we all) and forgot that the array was modified somewhere else. --Theo*

yeah that's why i did it that way :o it's just a quick hack to get KeyValueCoding compliance until i can clean up the rest of my code.

----

These two methods have different time complexities.  The second sends a single retain message to newArray, and a single release to myArray (which may cascade into a release for each object in the array, partially negating the argument).  The first removes every object from myArray and adds every object of newArray to myArray.

*Actually, I don't think the second will send a retain message to newArray. It *should* make a shallow copy of it, sending an extra retain to every object in it. Are you getting confused with the     -copy optimisation for immutable arrays?*

So there's a potential problem with mutable objects *in* the array too? --Theo

*i wouldn't call it a problem, more like 'that's the way it's always been**
----
I guess, the best thing to do for a NSMutableArray and NSMutableDictionary is :
    
-(void)setMyArray:(NSMutableArray *)newArray
{
    if (myArray != newArray)
    {
        NSArray* arr = myArray;
	 myArray = [newArray mutableCopy];
        [arr release];
    }
}
----
Wait, way back in the beginning someone said     setArray: makes one array reference another (or at least be externally modifiable). This isn't true, at least not on Tiger. I just tested it. Does that mean it's safe to do that now instead of copying arrays every time? I can see that creating a new array *may* involve less messaging, but it involves a whole new object being allocated. --JediKnil 

    
             New Array?   Actions
setArray:        No       Removes and releases all objects, adds and retains new objects
mutableCopy      Yes      Release old array (and all objects), creates new mutable array, adds and retains new objects


----
That someone was being nonsensical. The two snippets are equivalent:

    
NSMutableArray *a1 = [a2 mutableCopy];

NSMutableArray *a1 = [[NSMutableArray alloc] init];
[a1 setArray:a2];


----
One difference is that they are not equivalent if     a2 is nil. Therefore a     -setArray: accessor cannot be used as part of a     -dealloc implementation to destroy the instance variable. Whether this is a problem or not depends greatly on your personal style. 

----

It's very important to remember that in a method of the form:

    
- (NSArray *)myArray
{
    return _myArray;
}


that you are actually returning a reference to _myArray.  Just because you are returning an NSArray * does NOT prevent the caller from attempting to call NSMutableArray * methods on it, and potentially messing up your internal state.  This is analogous to using     reinterpret_cast<> in C++, or     if(obj is someMutableClass) { (obj as someClass).someMutatorMethod() }  in C#.

Long story short: if you are using a mutable collection in your class for some reason, and need to provide an accessor method to that data, always do the following, unless you *really* want the caller to be able to mess with the collection your object owns:

    
- (NSArray *)myArray
{
    return _myArray copy] autorelease];
}


----

Or unless you do this sort of thing by contract, i.e. if it says it's an [[NSArray then don't treat it as an NSMutableArray. Yes, it's easy to circumvent your contract there, but it's obvious that anybody trying such a thing is going out of their way to break your code, and as such when it breaks it is clearly their own fault. I would say that you shouldn't go the     [_myArray copy] route unless and until you know you need it. -- RobRix

----

If your caller is going to mess with the objects you give it, then it doesn't really matter whether you return a mutable or immutable array. Calling code can still modify the contents of an NSArray object by digging into the guts of its internal CFArray. When your code interfaces with other code, you *expect* that other code to uphold contracts, but as long as the other code has access to your address space, you cannot guarantee contract adherence no matter what you do.
----

Excellent point. Given those circumstances, be reasonable-- clients of your code are unlikely to be your enemy! -- RobRix

----

It is worthwhile to consider the difference between antagonistic code and mistaken code.

It's absolutely correct that you can't defend against antagonistic code in this situation no matter what you do. There's no real point in trying. However, mistaken code is something else. Consider the following code, written by someone who was merely a bit absent-minded:

    
id array = [obj returnImmutableArray];
[array addObject:foo];


This isn't too hard to write by mistake, and the compiler won't issue any warnings. Returning your mutable array will result in this code "working", whereas returning an immutable copy will result in an exception being thrown which will hopefully be discovered during testing.

I'm not necessarily saying that you should return a copy every time, but it's something to think about.

