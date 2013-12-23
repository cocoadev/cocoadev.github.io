---
layout: page
title: RandomNumber
---



RandomNumber topics:
[Topic]


----

Simple question, how do you create a random number?

*-- I was going to email someone for the answer but I thought this question might be a good one for other newbies like myself.* ; )

-- JohnDevor

Not exactly a Cocoa question, but if we're going to be catering to a larger newbie audience as it seems is the trend, then why not...

Note that you cannot create true random numbers on a computer because everything a computer does must be procedural (as programmers, we should all know this). So people create pseudo-random number generators to simulate random number choosing that will appear to humans to be truly random.

We can use the pseudo-random number methods declared in stdlib.h in the standard C library to get these numbers. These methods are rand() and srand().

Before any calls to rand() are made, you should use srand() (seed random) to help improve the apparent randomness of the results. A typical call looks like the following:
    
srand(time(NULL));

This obviously uses the time() function (which is in time.h) to make each seed unique depending on when the function was called in time. Once the generator is seeded, subsequent calls can be made to rand() to retrieve random numbers. rand() returns an int between the range of 0 to RAND_MAX (defined in stdlib.h) which is usually around INT_MAX or whatever that constant is. For random numbers within a certain range, then modulus operator becomes indispensible. Here are some examples of how to use this.
    
rand() % 50; //Gives a number between 0 and 49 inclusive.
(rand() % 50)+1; //Gives a number between 1 and 50 inclusive.
(rand() % 51)-25; //Gives a number between -25 and 25 inclusive.


For more information on rand(), srand(), and related functions, read 'man 3 rand' (note: on my machine at least, it will bring up the OpenSSL man page for it's rand() function, which isn't what I've used here. Just hit q and the C library page will load).

--KevinPerry

----

Technically you're wrong about a computer not being able to generate a truely random number. All you would need to do is sample an RF signal and then seed a random number generator with a sampled value. Then you could use feedback to select a differenent Hz to sample :-) 

----

You can get that kind of environmental noise randomness in OS X-- read from /dev/random or /dev/urandom. man 4 random:

The random device produces uniformly distributed random byte values of potentially high quality.

To obtain random bytes, open /dev/random for reading and read from it.

To add entropy to the random generation system, open /dev/random for writing and write data that you believe to be somehow random.

----

Ah, but who says that all the gobs of RF noise in the air are truly random. Interesting philosophical question.... is anything **truly** random at all in the grand scheme of things? It's all cause and effect... (ie, in an alternate universe exactly like ours, you and your twin in that universe would flip a coin the exact same way and come up with the exact same result every time... in theory).

----

In most cases you can make do with pseudo random numbers.  Certain applications such as encryption however require "real" random number.  Another application is compression, theoretically it should be impossible to compress large sets of "real" random numbers.  Beside adding additional hardware to your computer you can also obtain random numbers from certain websites such as this: http://random.org.

As to whether two coins flipped in identical/parallel universes would come up the same...  Well acording to quantum theory they may not.   Here's an interesting article (linked from random.org): http://www.nytimes.com/2001/06/12/science/12RAND.html?ex=1051243200&en=3c7d5133647db297&ei=5070.

-- SaileshAgrawal

all gobs of RF noise are not random in a serial sense, but a single sample of an arbitrary frequency used to select the frequency of another sample should be completely random. Now I'm not saying that you are guaranteed a particular distribution, but I highly doubt that there is some binding coherency to the entire electromagnetic spectrum with a temporal order of such magnitude!!!!!!

----

I'd like to point out that the method of getting random numbers within a given range described above is not accurate.  Because of the nature of most random number generators, using a modulus (%) greatly decreases the randomness of the numbers.  Hence the preferred method is:

    
(rand() / RANDMAX) * 50
//Generates a number between 0 and 50,
//Provided that  RANDMAX is defined as the maximum number
//that rand() can output


Note also that you are not restricted to using the built-in rand().  There are numerous other random number generators in existance.  Probably the most well known is the MersenneTwister, which is 4 times faster than rand().  More info about it is available at: http://www.math.keio.ac.jp/~matumoto/emt.html

-- OwenAnderson

----

Another note about the sample above. I agree "%" is a very poor way to get a random number, and the test against RANDMAX is much better. However, while the sample:

    
(rand() / RANDMAX) * 50


does generate an integer value between 0 and 50, the distribution is not even across this space. The values 0 - 49 will appear with relatively regular frequency, and the number 50 will show up very, very infrequently. Here's a sample program:

    
#include <libc.h>

#define MAXVALUE 5
#define ATTEMPTS 10000000

int main (int argc, const char * argv[]) {
	int i;
	int values[MAXVALUE+1];

	srand(time(0));
	bzero(values, sizeof(values));

	for (i = 0; i < ATTEMPTS; i++) {
		int value = (float)rand() / RAND_MAX * MAXVALUE;
		values[value] += 1;
	}

	printf("value\tfrequency\n");
	for (i = 0; i < MAXVALUE + 1; i++) {
		printf("%d\t%d\n", i, values[i]);
	}
	
    return 0;
}


Here's the sample results:

    
val	frequency
0	2000463
1	2002038
2	1998323
3	1998651
4	2000522
5	3


This is because you will only get your maximum value if rand() returns its maximum value. You might try rounding the value first to even the distribution, like so:

    
#include <libc.h>
#include <math.h>

#define MAXVALUE 5
#define ATTEMPTS 10000000

int main (int argc, const char * argv[]) {
	int i;
	int values[MAXVALUE+1];

	srand(time(0));
	bzero(values, sizeof(values));

	for (i = 0; i < ATTEMPTS; i++) {
		int value = rint((float)rand() / RAND_MAX * MAXVALUE);
		values[value] += 1;
	}

	printf("value\tfrequency\n");
	for (i = 0; i < MAXVALUE + 1; i++) {
		printf("%d\t%d\n", i, values[i]);
	}
	
    return 0;
}


but that doesn't work well either:

    
val	frequency
0	1000484
1	2000695
2	2000327
3	1998591
4	2000491
5	999412



And anyway, that rounding idea is barking up the wrong tree entirely. Normally you are interested in computing a random number between 0 and some value, so you can make some kind of random decision. For example, let's say I want a random number between 0 and 5. I do the following:

    
		int value = (float)rand() / RAND_MAX * (MAXVALUE + 1);
		if (value > MAXVALUE) value = 0;


This gives me the number between 0 and 5 that I'm looking for, and if for some reason the number 6 appears (rand() happened to return RAND_MAX) I just push it back into range by arbitrarily calling it 0. The full program is:

    
#include <libc.h>
#include <math.h>

#define MAXVALUE 5
#define ATTEMPTS 10000000

int main (int argc, const char * argv[]) {
	int i;
	int values[MAXVALUE+1];

	srand(time(0));
	bzero(values, sizeof(values));

	for (i = 0; i < ATTEMPTS; i++) {
		int value = (float)rand() / RAND_MAX * (MAXVALUE + 1);
		if (value > MAXVALUE) value = 0;
		values[value] += 1;
	}

	printf("val\tfrequency\n");
	for (i = 0; i < MAXVALUE + 1; i++) {
		printf("%d\t%d\n", i, values[i]);
	}
	
    return 0;
}


The results are:

    
val	frequency
0	1665343
1	1665495
2	1666906
3	1666385
4	1668487
5	1667384


I now get integer numbers between 0 and 5 w/ relatively even distribution, and no chance of a buffer overrun.

ScreenSaver programmers can use a handy macro SSRandomIntBetween to do this kind of thing. From ScreenSaverView.h:

    
static __inline__ int SSRandomIntBetween(int a, int b)
{
    int range = b - a < 0 ? b - a - 1 : b - a + 1; 
    int value = (int)(range * ((float)random() / (float) LONG_MAX));
    return value == range ? a : a + value;
}


-- MikeTrent


----

Thanks for all of your comments! I thought this was a lot simpler concept before, but now I've learned more about this subject, so thanks for your insights. And thank you WikiWiki!
----
Everybody now, "Thank you newbies!"
--JohnDevor

----

MikeTrent, I am willing to defend the modulo operator (%). I used your example code, and generated the random numbers with this line:
    
int value = rand() % (MAXVALUE +1);

This generated the following output:
    
val     frequency
0       1666740
1       1666928
2       1667595
3       1664894
4       1665665
5       1668178

-- EnglaBenny

I guess I stand corrected.  I was just repeating what I read in an old C book.  Maybe it was referring to a different rand() implementation than OS X uses.  Either way, I still think the RAND_MAX technique is preferable.  It is conceivable that the modulus (%) method could break some random functions.  On the other hand, there is no way that the RAND_MAX technique could break a function.  But it would appear not to make much of a difference, at least on Mac OS X. --OwenAnderson

The claim comes from older versions of rand() available on some systems. In these systems the least significant bits (the ones we're looking at with %) tended to produce less random results, and the solution to that problem was to divide by RAND_MAX. Dig around on Google if you're interested in the history. Now, it seems to be the case that Darwin's rand() is better than the "known to be poor" rand() of old. Can you rely on that in the future? Will you ever need to port your code to some other platform? 

But even more than that, I worry about where my number came from. Say I want to use mod to pick a random number between 0 and 3. I'm only using the last two bits of my value, and that's creepy. I'll need to make sure my random generator generates a fairly even distribution when these high bits are ignored. On the other hand, if I divide by RAND_MAX to pick a random number between 0 and 3, I am using all 32 bits of my random value to compute my 2 bit result. That makes me feel better. 

*
Interjection into old stuff -

You aren't using all 32 bits, you're just favoring most significant bits over least significant bits.  In the case of generating a random number between 0 and 3, inclusive,

    rand() % 4 uses only the bottom two bits.

    floor((float)rand() / RAND_MAX * 5) uses only the top two bits (ignoring edge cases that make little difference).  Those are the only ones that determine what quadrant of the result space     rand() falls into.  

Now think about selecting a number between 0 and 2, inclusive.  In the modulus method, each bit is equally important.  In the divide-by-RAND_MAX method, the least significant bits are much less important.  Any number that starts with 11.. in binary is in the top third of the space, so will result in a '2'.

-KenFerry

*

Finally, I don't think any of this really matters to screen saver twinkers and wannabe video game hackers. Do whatever you want. The issue of where random numbers come from matters more to people who are interested in a standard distribution from their generator function. And people who are really serious about it, such as cryptographers, are using other mechanisms for random number generation. -- MikeTrent

If you modulus by 6, you're using all the bits in the random number. It's only powers of 2 that are problems, neh? -- KritTer

Really? Aren't you just using the bottom 3 bits? Modulo is the remainder after division, not the division itself. -- MikeTrent

Yep, and you can't tell the remainder by looking at the bottom three bits. By analogy, the rule for determining remainder modulo 3 in decimal is to add all the digits together and see if **that's** divisible by three. e.g. 18 is, 28 isn't. Nor is 218, but 318 is. As is 1218. Et cetera. -- KritTer

----
Anyone interested in generating random numbers should take a look at NumericalRecipes or Knuth's seminal works on TheArtOfComputerProgramming  Or, check random.org, which has links to a number of good resources.
----

**The natural follow-up question is whether or not any arbitrary discussion of computer science as pertaining to the determinism of generated values is itself deterministic.**

----

    rand() % x is not uniformly distributed.      random() % x is (assuming     (RAND_MAX+1) % x == 0).  Check the man page for random().  It specifically states that all bits from it are usable, and that random() & 01 is a valid way to generate a random bit, unlike rand(), whose lower bits are cyclic.

-- AndresSantiagoPerezBergquist

----

How about the MersenneTwister?  It has a period of 2^19937-1!

New page [http://www.math.sci.hiroshima-u.ac.jp/~m-mat/MT/emt.html]
Old page [http://www.math.keio.ac.jp/~matumoto/emt.html]  

From the WebPage:

""What is Mersenne Twister?
Mersenne Twister(MT) is a pseudorandom number generator developped by Makoto Matsumoto and Takuji Nishimura (alphabetical order) during 1996-1997. MT has the following merits: 
It is designed with consideration on the flaws of various existing generators.
The algorithm is coded into a C source downloadable below.
Far longer period and far higher order of equidistribution than any other implemented generators. (It is proved that the period is 2^19937-1, and 623-dimensional equidistribution property is assured.)
Fast generation. (Although it depends on the system, it is reported that MT is sometimes faster than the standard ANSI-C library in a system with pipeline and cache memory.)
Efficient use of the memory. (The implemented C-code mt19937.c consumes only 624 words of working area.)""

-PhilipRiggs

----

I'd like to point out that there are two sets of random functions. srand and rand are the C standard functions, and srandom and random are the BSD-issued functions that the manpages recommend (check man random and man srand). also, there are sranddev and srandomdev, which read a seed value from /dev/random and call srand[om]() with it.

and to KevinPerry: those are functions, not methods.

*--boredzo*

----

A quick test of the method I added to the GeneratingRandomNumber page:

    
+ (long)randomLongFrom:(long)lower to:(long)upper{
	return (random() % (upper-lower+1)) + lower;
}


driven by

    
	int results[6];
	for (int i=0; i<6; i++){
		results[i] = 0;
	}
	for (int i=0; i<10000000; i++){
		long r = [DieModel randomLongFrom:1 to:6];
		results[r-1]++;
	}
	for (int i=0; i<6; i++){
		NSLog(@"r[%d] = %d",i+1,results[i]);
	}


produces

    
r[1] = 1667172
r[2] = 1667044
r[3] = 1667503
r[4] = 1665171
r[5] = 1667205
r[6] = 1665905


Mk I eyeball says those look good enough, though it would not be a bad idea to run a few real statistical tests on the output sequence.  Still, I am willing to believe the man page on random() - the claim that every bit is usable is a very strong one, and I rather hope they tested it well. -- ScottEllsworth
----
The claim that every bit is usable is not equivalent to the claim that you can use the modulo operator to get randomness out.

For example, let's say we have a function called     Random() which returns random numbers evenly distributed between 0 and 255. In other words, every call gives you eight bits of randomness, which each bit being usable for anything. So     Random() % 2 will give you a perfectly random bit, etc.

Now you want a random number from 0 to 254. So you write     Random() % 255. But you're paranoid and run a test and print a histogram and, lo and behold, the number 0 shows up twice as often as every other number in the set! This is because every number maps to itself except for 255, which maps to 0, so you have in effect two 0's and one of everything else coming out of the generator.

This is only a major problem if your range is similar to that of the generator's actual output range, and it won't show up at all if your modulus is an even factor of the generator's max value (plus one). If you try to generate a number from 0 to 2 by doing     Random() % 3, you'll discover that 0 shows up slightly more often, but probably not enough for most purposes. Even so, modulus-mased range reduction is something to be careful of when using random number generators.

----

Don't use srand(time(NULL)) (or srandom()). Instead, use sranddev() or srandomdev(), which will seed from /dev/random.

----

17 is the most random number.

----
Randomness is not just about the various frequencies. Consider 
    
int suckyrandom(int maxval){
    static int lastVal=0;
    int result=lastVal;
    if(++lastVal>maxval)lastVal=0;
    return result;
}

This will produce values evenly distributed, but is obviously not random. The interesting question is what is the probability that I get x, given that the last value is y (call it p(x,y)). Ideally you want this to be such that p(x,y)=p(x), ie you gain no information from knowing the last value (p(x) being the probability that your generator returns x. The low bits of rand() being cyclic means that they fail this almost completely.

----
I'm a researcher from Cornell working on several projects involving high performance computing.  There is a statement on this page that I want to disagree with.  There *are* computer events that are random.  Traditionally, applications fall into two categories: deterministic and non-deterministic.  Simply put, a deterministic program will always produce the same results each time it is run.  Definitions of a non-deterministic program vary, but clearly anything involving the outside world (including human input, network traffic, sensor readings, etc) can cause each program run to produce a different (i.e. random) result.  From a single application's perspective, you need not even have user input to affect the way your program runs.  Aspects of the OS such as page faulting can not be determined by your application, but can affect your application nonetheless... sometimes in subtle ways.  Typically, such effects are undesirable and when they surface they are known as heisenbugs because they are not always reproducible.  You might argue that each time all the conditions fall into place just right the program's run will be the same... however, the same can potentially be said of the universe we live in and is a non-starter for most mathematicians.

----
I was under the impression that newtonian phyisics is not compatible with the idea of random, and the only "Random" physical event we can observe relates to the distribution of decaying subatomic particles. Everything else falls under the cause-and-effect banner, and so-called random events just have a chain of effect too long and complex to follow or reproduce. I am not educated on the subject and this is just hearsay. So perhaps a definition of the word "Random" might be in order.
-Jeremy Jurksztowicz

----
Well, if you want a really unusual number, reseed your generator every time, using the current date and time, the location of all mouse clicks in the past ten minutes, hash of the user's name, log of the number of characters in the relative path to their home folder from your program, the last ten login dates (in milliseconds since they last launched Safari), the number of preference files updated in the last 27.3 days, and any other information you can get your hands on. (That was kind of fun)

*Random (physics) = pretty much impossible
*Random (programming) = does not look like a pattern, and has roughly equal result frequencies.

BTW, to avoid the "double-zero" problem of     Random() % 254, just recalculate the number if it is 255. And go ahead and call me an ignorant freak (or something else equally insulting), but I think that a random function that "looks good" is almost always good enough unless you actually do have to generate several thousand random numbers in the life span of a program. And yes, I know that sometimes you do. --JediKnil
----
The technique of simply re-calling the generator if it gives something that is out of range is a standard technique which eliminates this problem. Or more efficiently, you can calculate a maximum based on a factor from your modulus, use modulus if the number is under that maximum, and recalculate if it's over. You lose no randomness by doing this technique.

Your list of randomness is incomplete, by the way. There is a lot more than just "perfect" and "good enough for an arcade game". Probably most importantly, there is cryptographic randomness which falls in the middle, whose criterium is "impractical for an adversary to guess the number stream".

Whether a technique is good enough really depends on what you're doing with it. Using     Random() % 52 to seed a deck of cards when     Random() returns numbers from 0 to 63 is going to produce noticeably incorrect results and annoy your players (or allow them to cheat). Using a large portion of the result from     rand() (the std C function) for things like positioning is ok, but masking off the bottom bit and using it to figure out a coin flip is going to be distinctly non-random, probably enough to notice for your more attentive players. Using     random() in the same situation will be fine, but use     random() to generate SSL session keys and you're sunk.

