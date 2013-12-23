---
layout: page
title: KTMutableMatrix
---

See also DesignMatrix.

I have passed a varible KTMutableMatrix * clientClusterMatrix  when window is load, it receives Okay. But when I  try to obtain it  in later methods, 
I got AccessBadMemory debug message. It seems like that clientClusterMatrix can not be obtained by the "getter"

Any help would be appreciated.


Phyllis


    
// Note: KTMutableMatrix * clientClusterMatrix has been defined as a  
global variable in the .h file

-(void) windowWillLoad: (KTMutableMatrix*)cCMatrix
{     
      [super windowWillLoad];    
      clientClusterMatrix= [[KTMutableMatrix alloc] init];
      clientClusterMatrix= cCMatrix;  // <<<<-----received fine here
      ...	
      [clientClusterMatrix autorelease];
}

-(KTMutableMatrix*)getClientClusterMatrix
{
    return clientClusterMatrix;
}

-(void)displaySegInv
{     ...
      [self setSegNum:clientClusterMatrix by:clientWordNum]; //<<<<------crashes here
      printf("segNum is : %s", segNum);
      ...
}

-(void)setSegNum:(KTMutableMatrix*) matrix by:(int) wordNum
{      ...      
       if ([matrix objectAtCoordinates:j,i] !=nil && [matrix objectAtCoordinates:j,i] !=@"")
                    [tempArray addObject:[ matrix objectAtCoordinates:j,i] ];
       segNum =[tempArray count];
      ...
}




----

Are you sure you want to be calling [clientClusterMatrix autorelease]; at the end of the windowWillLoad method?  Unless you retained it somewhere else it will be deallocated almost immediately after the method finishes.  -- JamesCallender 

----
I noticed that and deleted that line too, but problem exists same :(
Help !!

Phyllis

----

    
clientClusterMatrix= [[KTMutableMatrix alloc] init];
clientClusterMatrix= cWArry;  // <<<<-----received fine here


I'm confused what you're doing here, and I'm guessing you might be too. You appear to be creating a new KTMatrix then immediately killing all references to it - a memory leak - then not retaining the new array. Perhaps you wanted:
    
clientClusterMatrix= [cWArry mutableCopy];

here? Except you're passing in an NSMutableArray *, not a KTMatrix *.

What are you attempting to achieve? (To be honest, I can't see why the code even compiles as you've written it, the pointer assignment should at least give a warning.)

-- KritTer
----
I appologize. It was copy&paste error. This was in my original code:
    
-(void) windowWillLoad: (KTMutableMatrix*)cCMatrix
{     
      [super windowWillLoad];    
      clientClusterMatrix= [[KTMutableMatrix alloc] init];
      clientClusterMatrix= cCMatrix;  // <<<<-----received fine here, but cannot 
                                                    get it through getter later on
      ...	
      
}
...

So I have to use mutableCopy whenever I pass around the KTMatrix object?

*No, you could just use -retain. It also depends on whether the code that calls -windowWillLoad: will release cCMatrix or not; it should do so, in which case you want one of the two! -- KritTer *

----
I finallly obtained the object by changing the getter method:
    
-(KTMutableMatrix*)getClientClusterMatrix
{
     clientClusterMatrix retain] autorelease]  ;

}

-(void) windowWillLoad: ([[KTMutableMatrix*)cCMatrix
{     
      [super windowWillLoad];    
      clientClusterMatrix= [[KTMutableMatrix alloc] init];
      clientClusterMatrix= cCMatrix;  
      ...	
     // [clientClusterMatrix autorelease]; // <<<<-----delete this line
}


Questions I don't understand:

1. why I can not autorelease clientClusterMatrix in the method which it is initiated? (It crashes if I have it released there)

*Because you're not initing it, you're initing a different matrix and then leaking it.*

2. If it is not released in (void)windowWillLoad:..., why I have to retain it in the getter?

*Because you should be retaining it in -windowWillLoad:*

Phyllis

My guess would be that you have to retain it in the getter because you're autoreleasing it in the getter. If you just autoreleased it in the getter without retaining it first then it would be released every time someone used the getter, and sooner or later its retain count would get to zero and it would be deallocated. You could just have the getter return it with no retain or release, unless it's important that the matrix be able to stick around after the object which it came from is gone. -- AngelaBrett

I'm still really disturbed by this line:
    
      clientClusterMatrix= [[KTMutableMatrix alloc] init];

You realise that this line is entirely superfluous and just leaking memory, right? -- KritTer
----
Thanks a lot, I fixed them.  And I still kept the retain/release in getter just to make sure it will be there. 

What a practice on memory management  :-))

Phyllis

    
-(void) windowWillLoad:  (KTMutableMatrix*)cCMatrix 
{
    [super windowWillLoad];   
    clientClusterMatrix =[cCMatrix mutableCopy];
}

-(KTMutableMatrix*)getClientClusterMatrix
{
    return  clientClusterMatrix retain] autorelease ]  ;
}


*Glad that's sorted then :) I've orphaned the page. -- [[KritTer *

