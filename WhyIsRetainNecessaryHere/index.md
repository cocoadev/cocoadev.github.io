---
layout: page
title: WhyIsRetainNecessaryHere
---

It doesn't seem like it should be necessary....

Folks; 

XCode2

I have an appContoller object and a General/FileSystemNode object. 
I call the General/FSNObj to get an array of file attributes built in theFSNObj. 

nodeArray is an instance variable in appController. 

    
//---NONWORKING appController Code---
[nodeArray removeAllObjects]; 
General/NSLog(@"General/FSNOBJAttrArrary Count: ,%d",General/[[FSNObj filesAttrArray] count]); ---> 20 
[nodeArray setArray: General/[FSNObj filesAttrArray]]; 
//I tried this     [nodeArray addObjectsFromArray: General/[FSNObj filesAttrArray]]; 
//I tried this      nodeArray = General/[FSNObj filesAttrArray]; 
General/NSLog(@"Arrary Count: ,%d",[nodeArray count]); ---> 0


//--WORKING appController Code---
[nodeArray release]; 
nodeArray = General/[[FSNObj filesAttrArray] retain]; 


Interestingly 
//--NONWORKING appController Code---
[nodeArray release]; 
[nodeArray setArray: General/[[FSNObj filesAttrArray] retain]]; 


My Questions:
1) What is release doing that removeAllObjects does not?
	- Where is this documented?

2) Why is the retain necessary?

3) Why does the assignment work but not the setArray?

Thank-you for your help!
Steve

----

You're missing some details, such as how you create nodeArray in the first place. But I'll try to answer your questions with what you have here.

*1) What is release doing that removeAllObjects does not? - Where is this documented?*

Release will decrement nodeArray's reference count and possibly destroy the object. removeAllObjects merely removes its contents, it doesn't destroy the actual array. Both of these methods are documented in Xcode's help, and say exactly this. **Point of order - it's in the Cocoa documentation, not "General/XCode Help", which is specific to the General/XCode application. Search Apple's reference library:** http://developer.apple.com/referencelibrary/index.html

*2) Why is the retain necessary?*

Whenever you get an object back from a method whose name does not include "alloc" or "copy", then you implicitly have a temporary object that will not stick around forever. If you want to use this object past the end of the current method, then you *must* either retain or copy it, otherwise it could potentially be destroyed while you're still hanging on to a reference to it.

*3) Why does the assignment work but not the setArray?*

Because first you do     [nodeArray release]. At this point, you are not allowed to touch the object that nodeArray points to, because it could have been destroyed. There is a key difference between a pointer and an object that you aren't understanding.

    nodeArray = General/[[FSNObj filesAttrArray] retain] <-- this line of code sets the nodeArray *pointer* to point to a new array object.

    [nodeArray setArray:General/[FSNObj filesAttrArray]] <-- this line of code fills the contents of the object *that nodeArray points to* with the contents of the array. It does *not* re-point nodeArray. In particular, if nodeArray doesn't already point to a valid General/NSMutableArray instance, this line will either crash or do nothing.

----
Thanks for your explanation!!

I want to make sure I have right understanding.

1) Containing the General/FSNObj logic in a separate class is good OO
2) Calling for an array of info as a returned result is good messaging methodology and good encapsulation
3) Given this I actually WANT to retain the result  {I guess I have an instinctive aversion to retain}

In this case, I want the result to be a datasource for a General/TableView so I want the retained result to be assigned to an instance var so I have the scope necessary to use it elsewhere in the class.

What is the best way of doing this?

1) Do I initialize the instanceVar, nodeArray, over and over again?  
(This doesn't "feel" good to me.  Should I get over it?)

2) Do I initialize once (init?) and then:   
    
nodeArray = General/[[FSNObj filesAttrArray] retain];         
//after this do i need to do:
 General/[[FSNObj filesAttrArray] release];  // this seems really wanky - why do I re-execute the method just to release it - also the result is a local var so wouldn't it be different?!
OR
[nodeArray release]                    //this seems wanky too 'cause I'm releasing an object which is different than the one I sent the retain.


I know I'm a newbie but I'm trying...
Thanks Again!
Steve
----
Even though it looks like     nodeArray is different than     [FSN<nowiki/>Obj filesAttrArray], they really are the same. Using     [nodeArray release] is the preferred method here (do NOT initialize over and over again, you are completely right about how this feels). However, if you are only init'ing the array once, and only using it in the class that created it, don't retain/release at all. Just use the initial retain count of 1 (it comes with the     alloc message), and release the array in your     dealloc method. And don't forget to call     [super dealloc] too. --General/JediKnil

**(corrected myself) Alloc only allocates a space in memory for the object. The object sending -init to a newly allocated object is responsible for retaining it. Only factory methods like**     General/[NSNumber numberWithInt:0] **returns a retained (and also autoreleased) object.**

----

Think of object assignment as giving a name to an object. So,     nodeArray = General/[FSNObj filesAttrArray] means "whenever I use nodeArray from now on, I mean whatever object got passed back by     filesAttrArray."

At this point, you could then do     [nodeArray retain], and later     [nodeArray release], without feeling uncomfortable. However, for convenience, the     retain method actually returns the object retained, so we could also do     nodeArray = [nodeArray retain], and it would have the same meaning. So we can save a line of code and do     nodeArray = General/[[FSNObj filesAttrArray] retain]. The take-home message is that these are identical because of what     retain promises to return.

----

OK this is GREAT info, at least for me!  But now I have a question:  I do this in applicationDidFinishLaunching:
    
myArray = General/[[NSMutableArray array] retain];   //my intent is to create a good 'working' datasource, or whatever, that will get assigned many different values


Elsewhere I do this:
    
...
   [myArray setArray:General/someObject someAccessor] retain;
...


Now at dealloc I simply do one [myArray release].

Is this the RIGHT way to do this?  But when all those retains that get executed what happens to the previously retained result?
If this is not right must I release myArray everytime?  But then I have 'lost' access to it right so I have to realloc again, yes?

I'm close to 'getting' this.  Thanks to any who can help me over this last hump...
Steve  (a different Steve than the original Steve)

----

*Collection objects retain the objects they contain. When objects are removed from the collection, they are released. You should not retain an object you're adding to a collection unless you want to keep a reference to it elsewhere. (Or, more specifically,) Don't retain an object just to immediately put it into a collection. [myArray addObject:[someObject someSelector]]; will do just fine. As you'll read elsewhere if you read up on memory management (which you should; these are all basic questions), if you're designing an object that references other objects (just like a mutable array does), it's your object's responsibility to retain the objects in which it has an interest. If you created your own container type, your container would be responsible for retaining - and properly releasing - its contained objects when they're added / removed or when your object itself is deallocated.*

    

General/NSNumber * myNum = General/[[NSNumber numberWithInt:0] retain]; // numberWithInt created an autoreleased object. We retained it in addition.

[someArray addObject:myNum]; // myNum is retained yet again because it's added to the collection someArray

[myNum release]; 
// We've released our own hold on myNum, but it's got two more retains, one from the someArray collection
// and one from the numberWithInt convenience method, which returns an autoreleased object

[someArray removeObject:myNum]; // myObject has been released yet again, is now a retained/autoreleased object again

// ... method ends and we no longer have a reference to myNum; it'll be destroyed during the next
//  autorelease, likely when the app is idle.



----

OK so what you are basically saying is that I don't need the retain in my example above:
...
   [myArray setArray:General/someObject someAccessor] retain;
...

This works!  What I guess confuses me is the doc's for setArray do not specify that it (setArray) releases all previous objects and retains all objects in new array.
Maybe I should have extrapolated that from the doc's for addObject because setArray is just a convenience for an enumerated bunch o' addObjects?  Or is it?
In any case, setArray works and is KVO aware so my bound outlet to myArray gets updated!!  Yea!
