---
layout: page
title: CheesyXOREncryption
---



Here's a CheesyXOREncryption scheme, which I call a "scrambling" scheme, since I don't like suggesting this is some kind of secure encryption. This snippet originally appeared in the ProtectingResources page

    
// scramble
// written by Michael Trent for www.cocoadev.com
// 
// scramble is a very cheesy function that scrambles a stream of binary data
// into a second buffer of identical size. the process can be reversed by
// reversing the inputs. you can also use the same buffer as both src and dst.
// key controls the specific stream used to scramble the data.
//
// BUGS:
//   key is only 32 bits, which is lame.
//   scramble uses random() as its generator function, which is lame
//   entire src buffers must be scrambled at once; it's not re-startable.

#include <stdio.h>
#include <stdlib.h>

void scramble(unsigned long key, const unsigned char *src, unsigned char *dst, size_t size)
{
    // seed our generator function
    srandom(key);

    // while data remains in src
    while (size) {
	// pull an unsigned 32-bit value
	unsigned long scrambler = random();
	
	// scramble!
	if (size > 4) {
	    *(unsigned long*)dst = *(unsigned long*)src ^ scrambler;
	    src += 4; dst += 4; size -= 4;
	} else {
	    unsigned char *scr = (unsigned char*)&scrambler;
	    while (size) {
		*dst++ = (*src++) ^ (*scr++);
		size -= 1;
	    }
	}
    }

    // clear our generator function
    srandom(0);
}


Note this function is fully symmetrical; if you call the function twice on the same buffer, that buffer will appear unchanged. Here's a cheesy test driver:

    
int main (int argc, const char * argv[])
{
    char input[30] = "Here is some data to scramble!";

    printf("%s\n", input);

    scramble('lame', input, input, 30);

    // doing this messes up my terminal. but you get the idea.
    //printf("%s\n", input);
    
    scramble('lame', input, input, 30);
    
    printf("%s\n", input);
    
    return 0;
}


This is regarded as unsafe as someone versed in exclusive-or encryption algorithms could make short work of this algorithm via brute-force, especially if they knew what the encrypted contents actually were. Really, the only twist here is that we're XOR-ing with a generating function, instead of just using the same scrambler (such as just using 'key') across the whole buffer. Unfortunately, we've picked a fairly obvious generating function: random().

You could probably shore this up by using a better generating function than random(), preferably one that can be seeded with more than 4-billion seeds.

-- MikeTrent

