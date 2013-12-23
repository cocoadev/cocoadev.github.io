---
layout: page
title: ComparingUntypedScalarValues
---

Okay, all. It's been a while since I've started a discussion, but I've got a bit of a problem that I can't seem to figure out.

In my most recent project, I decided to write a scalar set class to match the scalar array class I already wrote. This is dandy, but I'm running into a couple of problems with the ordering of items in the array.

Basically, I'm using ISO C's qsort/bsearch implementations to keep the set ordered for fast access. This approach means that I have to provide a sorting function which will compare between two const void * values.

The only thing is, I have no idea if it's possible to do this properly. Can I compare the values of void **s? Will it matter if they're different sizes? It's a bit of a problem.

Can anybody help me with this? I'm great with the ObjC stuff but pointers in C were lightly skipped over in my programming course so I still trip up there now and then.

-- RobRix

A pointer is just an integer referencing the first byte of the data in memory; it is thus unique unless you start storing both C structs and their first elements in the set, i.e.     
struct { int b; } a;
InsertElement(&a);
InsertElement(&a.b); // Same address in memory!

 -- KritTer

Yes... this I understand. Unfortunately that doesn't really help me much, but I could be missing something there.

Anyhow, to (hopefully) clarify a little...

    
static int		SCompare(const void *a, const void *b)
{
	if(a < b)
		return -1;
	else if(a > b)
		return 1;
	return 0;
}


That's what I've got right now. I'm comparing the addresses at the moment because dereferencing a and b will result in errors.

What I'm wondering is, will I get the expected results if I typecast a and b to long long * before I dereference and compare them? (I'm choosing long long because that way I'm not going to lose range with integral types.) Will this work for comparing things like doubles? Am I out of my mind to try this?

If this won't work, I have a couple of other cards up my sleeve (as usual), but I really would like a general set for scalar types. So, keeping in mind that *a might be a double, integer, pointer, et cetera, is this likely to work?

Addendum: Keep in mind that all I care about is what order I should place the items in; I don't need to know the values. I could use 'em as bitfields, if that would be helpful.

-- RobRix

Okay, I think I've answered my own question. Compile and run the following code:

    
#include <stdio.h>
#include <stdlib.h>
int SCompare(const void *a, const void *b);
int main(void);

typedef struct {
float blah;
float se;
} Moi;

int main(void)
{
void *a[4];
char z = 3;
unsigned y = 3;
Moi w;
Moi * v;
char x = 3;

w.blah = 3.14;
w.se = 0.3;

a[0] = &z;
a[1] = &y;
a[2] = &x;
a[3] = &w;

v = (Moi *)a[3];

        printf("%d\n", SCompare(a[0], a[1]));
        printf("%d\n", SCompare(a[1], a[2]));
        printf("%d\n", SCompare(a[2], a[3]));
        printf("%d\n", SCompare(a[3], a[0]));
        printf("%d\n", SCompare(a[2], a[0]));
        printf("%f\n", v->blah);
}

int             SCompare(const void *a, const void *b)
{
        long long *aa = (long long *)a;
        long long *bb = (long long *)b;
        if(*aa < *bb)
                return -1;
        else if(*aa > *bb)
                return 1;
        return 0;
}


and it should output the following:

    
1
-1
-1
1
-1
3.140000


bonus points if you can tell me why that fifth one (the comparison between the two chars that are equal to 3) isn't 0 (and yes, I know why it is :) I feel vindicated in making this page).

I think I'm going to go about fixing this by whipping up a union of a 64-bit bitfield and a void pointer and using that for the list instead of void *.

-- RobRix
----
OK, I'm not totally familiar with C all that much, but couldn't you have just made the parameters of the SCompare function two const long long **s instead of two const void **s? It automatically will typecast the void **s to long long **s instead of having to reassign them to new variables. I get the same output with this change as you indicated up above.

-- Anonymous

This would have been possible, except for how I'm using SCompare in the actual class; it has to be used by the C Library's qsort and bsearch functions, both of which require the void **s. And it still wouldn't work properly, because it'd be saying that x and z aren't equal (which they are).

-- RobRix

