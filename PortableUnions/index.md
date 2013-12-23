---
layout: page
title: PortableUnions
---



Since I can't test my UniversalBinaries on an x86 Mac, I hope someone can clarify how unions will align on x86.

I use this code to convert Finder createDates to an NSDate object:

    

// http://www.faqts.com/knowledge_base/view.phtml/aid/16144
NSDate* dateForJan1904() {              // utility to return a singleton reference NSDate
        static NSDate* Jan1904 = nil;
        if (!Jan1904) {
                Jan1904 = [[NSDate dateWithString:@"1904-01-01 00:00:00 +0000"] retain];
        }
        return Jan1904;
}

NSDate* convertUTCtoNSDate(UTCDateTime input) {
        NSDate* result = nil;
        union {
                UTCDateTime local;
                UInt64 shifted;
        } time;
        time.local = input;
        if (time.shifted) {
                result = [[[NSDate alloc] initWithTimeInterval:time.shifted/65536
                        sinceDate:dateForJan1904()] autorelease];
        }
        return result;
}



Will this union return the same results on an x86 Mac?

----

I can't test 'cause I don't have Mactel yet, but judging from

/Developer/SDKs/MacOSX10.4u.sdk/System/Library/Frameworks/
CoreServices.framework/Frameworks/CarbonCore.framework/Headers/UTCUtils.h,

at least it seems you need to swap the bytes inside the structure...

----

If your union elements are the same size and are single variables (as opposed to one element being a struct or array), then you have nothing to worry about.

Basically, if you're using a union to simply convert a bag of bits to a different type of the same size, as it appears that you're doing, everything is good. If you're using a union to access pieces of a variable, then you need to use #ifdefs to reverse the order of things on little-endian machines, or you need to switch to another technique.

In summary:
    
union {
   uint32_t intRepresentation;
   float floatRepresentation;
} // this is fine

union {
   struct {
      uint16_t highWord;
      uint16_t lowWord;
   } words;
   float value;
} // this is broken, highWord and lowWord need to be reversed on little-endian machines

union {
   uint16_t highWord;
   float value;
} // this is broken, you need to use a struct to pad the beginning of the union for highWord to work
  // or you need to switch to other techniques for extracting the data


----

I just ran this test and figured out that using unions to shift bits around is not a good idea.

    
	unsigned long long u64val = 0x123456789abcdefULL;
	union {
#if __BIG_ENDIAN__ 
		struct {
			UInt16 high;
			UInt32 mid;
			UInt16 low;
		} highmidlow;
		struct {
			UInt32 high;
			UInt32 low;
		} highlow; 
#else
		struct {
			UInt16 low;
			UInt32 mid;
			UInt16 high;
		} highmidlow;
		struct {
			UInt32 low;
			UInt32 high;
		} highlow; 
#endif
		unsigned long long u64;
	} u64union;
	u64union.u64 = u64val;
	
	NSLog(@"highmidlow: (high: %x mid: %x low: %x)", u64union.highmidlow.high, u64union.highmidlow.mid, u64union.highmidlow.low);
	NSLog(@"highlow: (high: %x low: %x)", u64union.highlow.high, u64union.highlow.low);

/*
    Output on PPC:
        highmidlow: (high: 123 mid: 89abcdef low: bff0)
        highlow: (high: 1234567 low: 89abcdef)
*/



If someone could run this code as a UniversalBinary on a Mac x86 that would be greatly appreciated.

*Um I don't think anyone besides Apple even has one yet. They're not even shipping yet to my knowledge.*

You never know if someone at Apple might see this.

I figured out why the alignments were off. For some reason the struct     highmidlow has a width of 12 bytes (but sizeof(UTCDateTime) is 8).
Does anyone know why these two structures are sized different?

    
/*
    UTCUtils.h defines UTCDateTime as:

struct UTCDateTime {
  UInt16              highSeconds;
  UInt32              lowSeconds;
  UInt16              fraction;
};
typedef struct UTCDateTime              UTCDateTime;

*/
struct UTCDateTimeTest {
  UInt16              highSeconds;
  UInt32              lowSeconds;
  UInt16              fraction;
};
typedef struct UTCDateTimeTest UTCDateTimeTest;



NSLog(@"sizeof(UTCDateTimeTest): %i sizeof(UTCDateTime): %i", sizeof(UTCDateTimeTest), sizeof(UTCDateTime));
// output -> sizeof(UTCDateTimeTest): 12 sizeof(UTCDateTime): 8

----

Structs get padded for fast access. A uint32_t must be on a four-byte boundary for optimal access (on most architectures, anyway, including PPC and x86); if it's not so aligned, then loading it from memory will be significantly slower. Because of this, the compiler will generally pad structs to make sure that every variable inside the struct is optimally aligned. This means that there will be two bytes of pad inserted between your low and mid elements to make sure that mid is aligned properly. How to avoid padding is compiler-specific, and generally a bad idea to rely on. This kind of union hacking is undefined in general, actually. Although you normally get the same bits back no matter which union element you access, the compiler is perfectly free to feed you random noise if you access a different union element than the one you used to store.

*In some cases, it is not only inefficient to access unaligned data, but it actually throws a hardware exception.  As for the size of UTCDateTime being 8 bytes instead of 12, I think the key would be the '#pragma options align=mac68k' in UTCUtils.h.*

Normally, if unaligned access throws a hardware exception, it will be caught by the OS and the behavior of the unaligned access emulated, so your code won't notice. This is, of course, glacially slow, which is a good reason to avoid it.

----

Another thing I'd like to add is that all the function in UTCUtils.h is marked DEPRECATED in Tiger.
I don't know the precise reason, but I suspect it's because of the things discussed above, e.g. intentionally non-aligned struct etc.

