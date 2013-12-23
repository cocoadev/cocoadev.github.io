---
layout: page
title: CFNumberMemoryLeakProblem
---

Memory Management Problem with CFNumber


My program uses a large number of floating point numbers which I wish to save so I use the following code:

    
- (id) init {
	if (self = [super init]) {
		int count = 1690;
		// create weightsArray and initialise randomly
		_weightsArray = (float *) calloc(count, sizeof(float));
		while (count > 0) {
			count--;
			_weightsArray[count] = fRandom(); //function producing random number
		}
	}
	return self;
}

- (void) encodeWithCoder:(NSCoder *) encoder {
	// encode various variables
	// .. .. ..
	[encoder encodeObject:[self encodedWeightsArray] forKey:PSNeuronWeightsKey];
	// .. .. ..
}

- (NSMutableArray *) encodedWeightsArray {
	NSMutableArray * weightsArray = [NSMutableArray arrayWithCapacity:0];
	int i;
	for (i = 0; i < 1690; i++) {
		[weightsArray addObject:[NSNumber numberWithFloat:_weightsArray[i]]];
	}
	return weightsArray;
}

I expect my NSNumber objects will be autoreleased.  When I run the program within ObjectAlloc to check for memory leaks, I see about 115,000 CFNumber objects being created during a save operation.  Some 70,000 of them are never released.

Since I never create CFNumber objects, I assume they are being created by NSNumber and I think NSNumber is not cleaning up after itself.  Incidentally NSNumber is never spotted by ObjectAlloc.

Thanks for any suggestions
Don McBrien

----

NSNumber is toll-free-bridged to CFNumber.  ObjectAlloc should give you a stack trace where a lot of those allocations happened.  Those should give you a pointer where to look.  (also, not much point in calloc'ing the array if you're going to be filling in every value immediately thereafter.  The zeroing portion is wasted work)

Also, does the number go up every time you save?  Or does it stay constant.  If it goes up, then there may be some issues that aren't obvious (maybe in the code that got removed).  If not, you may be looking in the wrong place.

----

Perhaps the autorelease pool hasn't gotten around to releasing your NSNumber objects. Try alloc/initing them and explicitly send release after you add it to your array.

Seconded:  try explicit allocs and releases just to see what happens.  

----

Yes, the number go up every time I save!!  That's the nub of my problem.  If I comment out the addObject line, there is no problem (nothing saved of course) so I think the removed code is not relevant.  Seems to suggest I'm looking in the right place.  Any idea why NSNumber is never reported by ObjectAlloc?
BTW I use calloc 'cos I never know what size the array will be.  count is a variable in the real program.
I've also tried alloc/initing/releasing NSNumber without solving my problem.

regards
Don

*You are allowed to use multiplication in your function calls :-)   malloc (sizeof(float) * count) will work just fine.*


----

NSNumber and CFNumber are the same thing.  ObjectAlloc is reporting your NSNumbers, just as CFNumbers.  CFNumber is the vanilla-C version of NSNumber, and they are completely interchangeable.  --OwenAnderson

----
The answer to this problem is likely that NSNumberRetainCountStartsAtTwo
It seems to be the case for intger     NSNumber, needs to be investigated for float... If this is for     isEqual: optimization, it might make some sort of practical sense for int in some situations, but for float??

