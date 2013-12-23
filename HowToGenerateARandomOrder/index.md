---
layout: page
title: HowToGenerateARandomOrder
---



Here's a function that generates a random order. This function takes three arguments. The first argument is to set the random order count. The second argument is to set the string width of the values placed in the random order. The third and last argument is the data type you wish to get back as an array of random objects (dataType==0 returns an array of NSStrings, dataType==1 returns an array of NSNumbers, dataType==2 returns the raw range data used to sort the order).

A function like this is useful if you want to create a random play list of mp3's. 

If you set the padding value to zero, no padding will take place if the dataType is set for an array of strings. If you set the padding value to 3 the width of all strings will be 3 unless the count produces a string value with a width greater than 3 (in cases like this the padding value becomes the max width produced by the count). For example if you set the count to 101 and you set the padding value to 2, the max width will be 3 for the return value of 100, in which case the function will adjust the padding value to 3 to accommodate the max width. 

this:
    
    id randomOrder=UmRandomOrder(12, 3, 0);
    NSLog(@"%@", randomOrder);


outputs this: 

    
2003-07-26 15:49:48.423 test[6135] (009, 010, 007, 006, 003, 000, 001, 002, 004, 011, 005, 008)


here's the source:

    
#include <sys/time.h>

int UmRangeLengthCompare(const  void  *ptr1,  const  void *ptr2) {
    NSRange *range1=(NSRange *)ptr1;
    NSRange *range2=(NSRange *)ptr2;
    if (range1->length < range2->length)
        return NSOrderedAscending;
    else if (range1->length > range2->length)
        return NSOrderedDescending;
    else
        return NSOrderedSame;
}

id UmRandomOrder(int count, int padding, int dataType) {
    int lastMicrosecond;
    struct timeval tv;
    id randomOrder=[NSMutableArray array];

    int MAX_COUNT=16777216; // set to any value you wish up to 2^32
    if (count<0 || count>MAX_COUNT) {
        NSLog(@"count: %i out of bounds: (0-%i)", count, MAX_COUNT);
        return randomOrder;
    }
    if (!(dataType>=0 && dataType<3)) {
        NSLog(@"invalid data type: dataType=0 (NSString), dataType=1 (NSNumber), dataType=2 (NSData)");
        return randomOrder;
    } 
    
    ////////////////////////////////////////////////
    // 
    //  The seed value is generated here. 
    //  gettimeofday() provides the time in 
    //  microsecond from Jan 1, 1970 to present. 
    //
    //  this block loops until at least two 
    //  microseconds have passed to insure that
    //  a unique seed value has been obtained.
    //  The uniqueness of this seed value is only
    //  valid for about one hour, meaning that the same
    //  seed value may have been used one
    //  hour prior to this one and the same seed
    //  value may be used one hour from now.
    //  The number of available seed values
    //  for any one hour time interval is 2^32
    //  therefore the chances of generating two equal
    //  seed values back to back is 1 in 4,294,967,296.
    //  This statistic assumes that this function
    //  is called in a random fashion, so if 
    //  a random order is needed in precise intervals of 
    //  68 minutes and 16.000000 seconds
    //  a state variable and modification
    //  to this function will be required (but I highly
    //  doubt this will ever be the case).
    //   
    ////////////////////////////////////////////////
        
    gettimeofday(&tv, NULL);
    do {
        lastMicrosecond=tv.tv_usec;
        gettimeofday(&tv, NULL);
    } while (lastMicrosecond==tv.tv_usec);
    int seed=tv.tv_sec<<20;
    seed+=tv.tv_usec;
    srand(seed);
    
    ////////////////////////////////////////////////
    // 
    //	An array of range values is created here. 
    //  Each range value has it's location set  
    //  to the order number that the random number was
    //  generated and it's length set to a 
    //  random generated number
    //  
    ////////////////////////////////////////////////
    
    id rangeData=[NSMutableData dataWithLength:count*8];
    NSRange *rangePtr=(NSRange *)[rangeData bytes];
    NSRange *firstRange=rangePtr;
    NSRange range;
    int i;
    for (i=0;i<count;i++) {
        range.location=i;
        range.length=rand();
        *rangePtr=range;
        rangePtr++;
    }
    
    ////////////////////////////////////////////////
    //  
    //  The array of ranges are sorted based on length 
    //
    ////////////////////////////////////////////////
    
    qsort(firstRange, count, 8, UmRangeLengthCompare);
    
    ////////////////////////////////////////////////
    //  
    //  The return type depends on the third 
    //  argument (dataType)
    //  
    //  dataType==0:
    //       returns an array of NSStrings with
    //       a width based on the padding value
    //       provided by the second argument (padding)
    //  
    //  dataType=1:
    //       returns an array of NSNumbers 
    //       
    //  dataType=2:
    //       returns the raw range data (NSData)
    //
    ////////////////////////////////////////////////    
    
    rangePtr=firstRange;
    if (dataType==0) {
        char numBuf[20];
        sprintf(numBuf, "%i", count);
        int maxWidth=strlen(numBuf);
        if (maxWidth>padding && padding>0) padding=maxWidth;
        if (padding<1) padding=0;
        else if (padding>10) padding=10;
        id formatString=[NSString stringWithFormat:@"%c0%ii", '%',  padding];
        for (i=0;i<count;i++) {
            range=*rangePtr;
            rangePtr++;
            [randomOrder addObject:[NSString stringWithFormat:formatString, range.location]];
        }
        return randomOrder;
    }
    else if (dataType==1) {
        for (i=0;i<count;i++) {
            range=*rangePtr;
            rangePtr++;
            [randomOrder addObject:[NSNumber numberWithInt:range.location]];
        }
        return randomOrder;
    }
    else if (dataType==2) return rangeData;
    return randomOrder;
    
}


You can have this function return the raw data used to sort the random order if you don't need NSString or NSNumber objects. To use the raw data just cast the bytes to (NSRange *):

    
    int orderCount=10;
    int dataType=2;         // dataType=0 (NSStrings), dataType=1 (NSNumbers), dataType=2 (NSData)
    int stringWidth=0;      // this value does nothing if you ask for the raw sort data
    id sortData=UmRandomOrder(orderCount, stringWidth, dataType);
    NSRange *rangePointer=(NSRange *)[sortData bytes];
    int i;
    int randomIndex;
    for (i=0;i<orderCount;i++) {
          randomIndex=rangePointer[i].location;
          NSLog(@"randomIndex: %i withBounds: (0-%i)", randomIndex, orderCount-1);
    }


--zootbobbalu

----

For those of you not afraid to mix CeePlusPlus with ObjectiveC then random_shuffle might be a lot easier to use. And it even comes with this nice guarantee:

*
This algorithm is described in section 3.4.2 of Knuth (D. E. Knuth, The Art of Computer Programming.  Volume 2: Seminumerical Algorithms, second edition.   Addison-Wesley, 1981). Knuth credits Moses and Oakford (1963) and Durstenfeld (1964).  Note that  there are N! ways of arranging a sequence of N elements.  Random_shuffle  yields uniformly distributed results; that is, the probability of any particular ordering is 1/N!.  The reason this comment is important is  that there are a number of algorithms that seem at first sight to  implement random shuffling of a sequence, but that do not in fact produce a uniform distribution over the N! possible orderings.  That is, it's easy to get random shuffle wrong.
*

----

Interesting... I'm open to changing the algorithm that creates the random shuffle. I mainly wanted to create a function that supplies an NSArray filled with padded NSStrings or an array filled with NSNumbers. I have to admit that I didn't do a histogram of the generated output (looking at the individual distributions of course grained segments). Since I initially intended this function to be used in non critical situations, like shuffling the order of a playlist, I didn't really give the quality of the random shuffle much thought. I did pay close attention to insuring that a unique random seed would be created each time the function is called (except for the rare case documented in the source). I also wanted to do this this in C, so the example could easily be cut and pasted into a method implementation of your choice (personally I don't think CeePlusPlus should be mixed with ObjectiveC). 

Like I was saying, I'm open to changing the algorithm that creates the random shuffle. --zootbobbalu

---

If the random_shuffle routine is part of the STL (Standard Template Library), you can grab the code and convert it to C/ObjectiveC
as required.  -- Bruce

See http://www.sgi.com/tech/stl/random_shuffle.html for some additional information.

---

Or if you have developer tools installed, look here:
    
/usr/include/gcc/darwin/3.1/g++-v3/bits/stl_algo.h

The algorithm is btw linear in time, where as the above seems to use a bubble sort (which is quadratic).

----

In response to not mixing CeePlusPlus and ObjectiveC, you're missing out on a lot of features! And e.g. the algorithms present in STL can be used with POD, so you wouldn't even involve the object oriented features of CeePlusPlus (even though these are just extended structures, and you already mix ANSI-C's struct's with ObjectiveC's @interface's).

----

The only reason I like to stick with C or ObjC is because this is a forum for Cocoa. I personally like the fact that we don't mix CeePlusPlus and Java in examples here. I'm aware that using a straight insertion sorting algorithm is the worst choice, but I just wanted to keep things simple. 

One thing I noticed with the random_shuffle function is that you shouldn't use this function blindly because it looks like you still have to seed the random function generator properly. I would love use this function though, but if I do, I think it will only be fair to the green Cocoa programmers that visit this site to be able to read this post without having to struggle through any off the wall issues (like how to include C++ source in an M file - which I haven't even done yet). To do this right, we need to document how to mix C++ with ObjC, and I don't mean just saying add another "m" to file extensions. I noticed that in the CeePlusPlus discussion that the STL framework is briefly mentioned. I would be willing to write up the topic that explains how to incorporate this framework into Cocoa if you guys help me out with a couple of things:


*What are the ramifications of using C++ libraries when building bundles and frameworks (especially embedded frameworks).
*What kinds of conflicts can come about when mixing C++ with ObjC. Are there any namespace issues? 
*What is the proper way to include stl_algo.h? I tried to add this file into a project but PB complains about not being able to find supporting files. Could someone give a simple example in the form of a tool (e.g. random_shuffle_main.mm). The sgi example would be a nice place to start.

    
const int N = 8;
int A[] = {1, 2, 3, 4, 5, 6, 7, 8};
random_shuffle(A, A + N);


*Are there any extra gcc flags that need to be added?


--zootbobbalu

---

The proper header to include is algorithm (without .h). That should be all which is required -- I was the one who added the CeePlusPlus and GenericProgramming pages, I know this is ObjectiveC (and not CeePlusPlus), but I make heavy use of CeePlusPlus myself (mixed with ObjectiveC), and I am often asked about the advantages, so I figured it was time to try and document some of them, and believe me, there are a lot.

I have added an ObjectiveCeePlusPlus page, but still work in progress (as I am a little busy right now).

----

    
#include <Carbon/Carbon.h>
#import <Cocoa/Cocoa.h>
#include "algorithm"
#include "stl_algo.h"

int main(int argc, char *argv[]) {
    NSAutoreleasePool *pool=[[NSAutoreleasePool alloc] init];
    NSFileManager *manager=[NSFileManager defaultManager];
    
    int i;
    const int N = 8;
    int A[] = {1, 2, 3, 4, 5, 6, 7, 8};
    random_shuffle(A, A + N);
    char buf[1024];
    for (i=0;i<8;i++) {
        sprintf(&buf[i*4], "%03i ", A[i]);
    }
    NSLog(@"%s", buf);
    
    [pool release];
    return 0;
}


This gives the following error:

**`random_shuffle' undeclared (first use this function)**

You say that you only have to include "algorithm" without the "h" extension, but my build still failed because I didn't include "stl_algo.h". Even after including "stl_algo.h" I get the error above. Can you try to get this main function to compile? --zootbobbalu

Don't include stl_algo.h, this is a private header -- but you must prefix random_shuffle with std:: (or put "using namespace std;" somewhere in your file).

----

OK, everything works fine, but...


*"mm" files talk about three times longer to compile than "m" files
*If C++ is going to be a useful addition to Cocoa projects, we need to document a good strategy for incorporating object files create from "mm" source files. Is it better to build a specialized framework to isolate "mm" files, or is there a clever way to include C++ source without substantially increasing turnaround times. 
*One a side note, random_shuffle() will produce the same random order even if you change the seed value with srand(). How can you change the seed value?


Thanks!!
--zootbobbalu

---

Yes, I wrote about both the name space and compile times at the ObjectiveCeePlusPlus page I added (on your request)!

With regard to isolation, I try to write most of my code as platform neutral ISO C++ and then only have a few .mm files (which basically is the view part) -- this makes it easier to port and test model code in isolation.

But I really hope compile times will improve, it currently takes 17 minutes to "make clean" and compile a project I am working on (with full optimisations though) -- which have seriously got me started thinking about writing a compiler myself.

----

I think this should do what you want.  The algorithm is O(n), and runs on an arbitrary NSMutableArray.

    
#import <Cocoa/Cocoa.h>

(void)randomizeArray:(NSMutableArray *)array
{
	// Assume that the random number system has been seeded.
	int i, n = [array count];
	for(i = 0; i < n; i++) {
		// Swap the ith object with one randomly selected from [i,n).
		int destinationIndex = random() % (n - i) + i;
		[array exchangeObjectAtIndex:i withObjectAtIndex:destinationIndex];
	}
}


This function is more general than the order function you described above, and could solve the same problem by passing an array of integers.

----

I've thought about updating this page after I started doing a similar thing, but I never got around to it. 
Now I just make a mutable copy of the array and do the following.

    

	NSMutableArray *randomQueue = [NSMutableArray arrayWithArray:someArray];
	int count = [randomQueue count];
	sranddev();
	while (count--) {
		int randomIndex = rand() % (count + 1);
		id object = [randomQueue objectAtIndex:randomIndex];
		/* 
			do something with the object
		*/
		[randomQueue removeObjectAtIndex:randomIndex];
	}


Basically the same thing that you added above. --zootbobbalu

*This is nicely randomized, but it has poor performance characteristics. Removing an object from a random point in an array is O(n) to the size of the array, and so this code will run in O(n^2) time.*

TRUE!! I forgot why I started this discussion in the first place (I posted this more than two years ago). I like your exchange method for large random permutation sets. I just updated a framework that is shared across a bunch of projects to include this improved random index generator.

    
@interface RandomIndexPermutation : NSObject {
	int *indices;
	unsigned count;
	int index;
}
+ (id)permutationWithCount:(unsigned)cnt;
- (id)initWithCount:(unsigned)cnt;
- (int *)indices;
- (int)next;
- (unsigned)count;
- (BOOL)isValid;
- (void)remix;
@end

@implementation RandomIndexPermutation
+ (id)permutationWithCount:(unsigned)cnt {
	return [[[RandomIndexPermutation alloc] initWithCount:cnt] autorelease];
}
- (id)initWithCount:(unsigned)cnt {
	self = [super init];
	if (self) {
		indices = malloc(cnt * sizeof(int));
		count = cnt;
		for (index = 0; index < count; index++) indices[index] = index;
		[self remix];
	} 
	return self;
}
- (void)remix {
	srandomdev();
	for (index = 0; index < count; index++) {
		int swap = indices[index];
		int destinationIndex = random() % (count - index) + index;
		indices[index] = indices[destinationIndex];
		indices[destinationIndex] = swap;
	} 
	index = 0;
}
- (unsigned)count {return count;}
- (int *)indices {return indices;}
- (int)next {
	if (index < count) return indices[index++];
	return NSNotFound;
}
- (BOOL)isValid {
	int i;
	NSMutableSet *set = [NSMutableSet set];
	for (i = 0; i < count; i++) [set addObject:[NSNumber numberWithInt:indices[i]]];
	return ([set count] == count) ? YES : NO;
}
- (void)dealloc {
	free(indices);
	[super dealloc];
}
@end



As a side note, I still like my method of removing random objects from a queue since you don't have to keep track anything. For small arrays this seems like the better choice, but I do like the exchange method for large index sets. --zootbobbalu

