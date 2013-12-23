---
layout: page
title: OrAddition
---

Using a scheme where enum values increase by the power of two


A = 1
B = 2
C = 4
D = 8
blah blah

And I see a number like 5, what's the fastest way for me to know that I'm looking at A+C?

    
-(NSString *)convertNumber:(int)number {
   
    id answer=[NSMutableString string];
    
    if (number & 0x1) [answer appendString:@"A"];
    if (number & 0x2) [answer length] ? [answer appendString:@" + B"] : [answer appendString:@"B"];
    if (number & 0x4) [answer length] ? [answer appendString:@" + C"] : [answer appendString:@"C"];
    if (number & 0x8) [answer length] ? [answer appendString:@" + D"] : [answer appendString:@"D"];

    return answer;

}

or

-(NSString *)convertNumber:(int)number {

    id answer=[NSMutableString string];
    int powerOfTwo=1;
    int i;
    BOOL appendmentMade=NO;
    for (i=0;i<26;i++) {
        if (number & powerOfTwo) {
            if (appendmentMade) [answer appendFormat:@" + %c", i+65];
            else [answer appendFormat:@"%c", i+65];
            appendmentMade=YES;
        }
        powerOfTwo=powerOfTwo << 1;

    }
    return answer;

}




--zootbobbalu

----

Subjectively, the first looks faster, but this is no substitution for testing/profiling. -- RobRix

----

I agree that the first way is more inline, but the second is more compact. I guess it really depends on how much you want to optimize this. --zootbobbalu

----

I'd go with the second, not because it's faster, but because it'd be far easier to optimize further if you found out your initial implementation wasn't fast enough.  Loop unrolling usually only provides a tiny performance enhancement on modern processors, and the compiler is usually far smarter about when it'll help than the programmer.  From my perspective, the low-hanging fruit in optimizing your algorithm seems to be:

*Eliminating all the temporary NSString objects you're creating and the appending operations to your NSMutableString, and using a temporary buffer instead.
*Not testing if it's the first character on every match.  I'd use 2 loops, one loop that iterates until it finds the first match and then just appends the character, and then a second loop for all the other matches that inserts the " + " and then the character.  At the very least, you'd want to replace that method call with an isFirstChar local variable.

So, without further ado, my entry for a fast OrAddition decoder:
    
#define kFirstLetter 'A'
#define kLastLetter 'Z'

- (NSString*)convertNumber:(int)number
{
	char* charBuf = alloca(sizeof(char) * (kLastLetter - kFirstLetter) * 4);
	int bufIndex;
	char currChar;
	int powerOfTwo = 1;
	for (currChar = kFirstLetter; currChar <= kLastLetter; currChar++) {
        if (number & powerOfTwo) {
			charBuf[bufIndex++] = currChar;
			powerOfTwo *= 2;
			currChar++;
			break;
		}
		powerOfTwo *= 2;
	}
	for (; currChar <= kLastLetter; currChar++) {
		if (number & powerOfTwo) {
			charBuf[bufIndex++] = ' ';
			charBuf[bufIndex++] = '+';
			charBuf[bufIndex++] = ' ';
			charBuf[bufIndex++] = currChar;
		}
		powerOfTwo *= 2;
	}
	return [NSString stringWithCString:charBuf length:bufIndex];
}

You could probably make it considerably faster using some sort of table lookup scheme or using Altivec instructions, but this will probably do if it's only going to be called thousands of times a second instead of millions. ;)  -- Bo

----

Yes I'm really bored today :-)

    

convertNumber4(int n, char *buf) {

    int p=1;
    buf[0]=0;
    char *secondChar=buf;
    secondChar++;
    int *ptr=(int *)secondChar;
    int length=0;
    int sps=32 << 24;
    sps+=43 << 16;
    sps+=32 << 8;
    BOOL a=NO;
    if (n & p) a ? (*ptr=sps+'A',ptr++) : (buf[0]='A',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'B',ptr++) : (buf[0]='B',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'C',ptr++) : (buf[0]='C',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'D',ptr++) : (buf[0]='D',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'E',ptr++) : (buf[0]='E',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'F',ptr++) : (buf[0]='F',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'G',ptr++) : (buf[0]='G',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'H',ptr++) : (buf[0]='H',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'I',ptr++) : (buf[0]='I',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'J',ptr++) : (buf[0]='J',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'K',ptr++) : (buf[0]='K',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'L',ptr++) : (buf[0]='L',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'M',ptr++) : (buf[0]='M',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'N',ptr++) : (buf[0]='N',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'O',ptr++) : (buf[0]='O',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'P',ptr++) : (buf[0]='P',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'Q',ptr++) : (buf[0]='Q',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'R',ptr++) : (buf[0]='R',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'S',ptr++) : (buf[0]='S',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'T',ptr++) : (buf[0]='T',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'U',ptr++) : (buf[0]='U',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'V',ptr++) : (buf[0]='V',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'W',ptr++) : (buf[0]='W',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'X',ptr++) : (buf[0]='X',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'Y',ptr++) : (buf[0]='Y',a=YES); p=p<<1;
    if (n & p) a ? (*ptr=sps+'Z',ptr++) : (buf[0]='Z',a=YES); p=p<<1;        
    *ptr=0;

}



This one requires a buffer that already exists before the call is made. Here the output:

    
void main(int argc, char *argv[]) {
NSAutoreleasePool *pool=[[NSAutoreleasePool alloc] init];
    int i;
    char buf[1024];
    NSLog(@"start");
    for (i=0;i<16777216;i++) {
        convertNumber4(i, buf);
    }
    NSLog(@"stop");
    [pool release];
    return 0;

}

2003-06-09 15:54:15.112 TEST[2313] start
2003-06-09 15:54:30.008 TEST[2313] stop   << about 15 sec on a 450 MHz GeeFour Cube



I'm done, someone else can try an Altevec version...... --zootbobbalu

----

That's pretty fast all right.  If I called mine that many times in a row, I'd probably run the system out of memory or choke the autorelease pool, whichever comes first.  -- Bo

----

After watching the Cup Finals I played around with this a couple of more times (I'm not a big Ducks fan but I can't stand New Jersey, AHHHHHH),  I got rid of checking to see if an initial letter has been appended yet (in every line that masks the number) and reduced the time from about 15 seconds to about 11.5 seconds. 

    

char *convertNumber5(int n, char *buf) {
    int p=1;
    int *ptr=(int *)buf;
    int sps=539697152;
    if (n & p)(*ptr=sps+'A',ptr++); p=p<<1;
    if (n & p)(*ptr=sps+'B',ptr++); p=p<<1;
    if (n & p)(*ptr=sps+'C',ptr++); p=p<<1;

    Blah Blah Blah

    if (n & p)(*ptr=sps+'Z',ptr++); p=p<<1;
    *ptr=0;
    if (buf[1]=='+') return &buf[3];
    else return buf;
}

 

I also iterated this code and saw a speed hit of about twice as many cycles from 11.5 seconds to about 21 seconds:

    

char *loopedConverter(int n, char *buf) {
    int i;
    int p=1;
    int *ptr=(int *)buf;
    int sps=539697217;
    for (i=0;i<26;i++) {
        if (n & p)(*ptr=sps+i,ptr++); p=p<<1;
    }
    *ptr=0;
    if (buf[1]=='+') return &buf[3];
    else return buf;

}



I did make a converter that used switch statements which reduced the conditional statement count by a factor of one sixth. Jumping around did help a little bit, so the best time so far is a hair under ten seconds. Here's a piece of it: 

    

    switch (n & 0x3F) {
        case (1) : {*p=s+'A',p++;break;}
        case (2) : {*p=s+'B',p++;break;}
        case (3) : {*p=s+'A',p++;*p=s+'B',p++;break;}
        case (4) : {*p=s+'C',p++;break;}

        Blah, Blah, Blah.......

        case (63) : {*p=s+'A',p++;*p=s+'B',p++;*p=s+'C',p++;
                        *p=s+'D',p++;*p=s+'E',p++;*p=s+'F',p++;break;}
    }
    n=n>>6;
    switch (n & 0x3F) {
        case (1) : {*p=s+'G',p++;break;}
        case (2) : {*p=s+'H',p++;break;}
        case (3) : {*p=s+'G',p++;*p=s+'H',p++;break;}
        case (4) : {*p=s+'I',p++;break;}

        Blah, Blah, Blah.......

        case (63) : {*p=s+'G',p++;*p=s+'H',p++;*p=s+'I',p++;
                        *p=s+'J',p++;*p=s+'K',p++;*p=s+'L',p++;break;}
    }
    n=n>>6;

    Blah, Blah, Blah.......
      


I guess you could make a switch stament with sixty seven million cases to really optimize this, but that would be silly. Oh yeah, a function call that immediately returns takes about one second on my machine.

So if 16 million conversion takes about ten seconds, that should be about 268 clock ticks per conversion (normalized to my 450MHz processor). I wonder how much better an Altivec version would do, because it looks like the real speed hit is caused by the branching. The best time was achieved by five switch statements and one conditional. I've only optimized with Altevec once, and that was for doing a bilinear transformation. This is about as close to the "metal" I want to get though. This IS a wiki for Objective C :-)   --zootbobbalu 

----

Very nice.  I couldn't even beat your earlier time, but that didn't stop you from improving it.  The only real speedup I could figure out on your unrolled loop was to assign with a ' + ' appended and then clear out the extra characters at the end to avoid the second branch.  The code follows:  -- Bo
    
void convertNumber6(int inNum, long* longBuf)
{
	char* charBuf = (char*)longBuf;
	if (inNum & (1<<0)) *(longBuf++) = 'A + ';
	if (inNum & (1<<1)) *(longBuf++) = 'B + ';
	if (inNum & (1<<2)) *(longBuf++) = 'C + ';
	if (inNum & (1<<3)) *(longBuf++) = 'D + ';
	if (inNum & (1<<4)) *(longBuf++) = 'E + ';
	if (inNum & (1<<5)) *(longBuf++) = 'F + ';
	if (inNum & (1<<6)) *(longBuf++) = 'G + ';
	if (inNum & (1<<7)) *(longBuf++) = 'H + ';
	if (inNum & (1<<8)) *(longBuf++) = 'I + ';
	if (inNum & (1<<9)) *(longBuf++) = 'J + ';
	if (inNum & (1<<10)) *(longBuf++) = 'K + ';
	if (inNum & (1<<11)) *(longBuf++) = 'L + ';
	if (inNum & (1<<12)) *(longBuf++) = 'M + ';
	if (inNum & (1<<13)) *(longBuf++) = 'N + ';
	if (inNum & (1<<14)) *(longBuf++) = 'O + ';
	if (inNum & (1<<15)) *(longBuf++) = 'P + ';
	if (inNum & (1<<16)) *(longBuf++) = 'Q + ';
	if (inNum & (1<<17)) *(longBuf++) = 'R + ';
	if (inNum & (1<<18)) *(longBuf++) = 'S + ';
	if (inNum & (1<<19)) *(longBuf++) = 'T + ';
	if (inNum & (1<<20)) *(longBuf++) = 'U + ';
	if (inNum & (1<<21)) *(longBuf++) = 'V + ';
	if (inNum & (1<<22)) *(longBuf++) = 'W + ';
	if (inNum & (1<<23)) *(longBuf++) = 'X + ';
	if (inNum & (1<<24)) *(longBuf++) = 'Y + ';
	if (inNum & (1<<25)) *(longBuf++) = 'Z + ';
	if (longBuf == charBuf)
		*longBuf = 0;
	else
		longBuf[-1] = longBuf[-1] & 0xFF000000;
}


PS  One thing that surprised me was how much improvement compiler optimizations provided.  Your earlier algorithm dropped from 21 to 7 seconds on my tibook just by switching from -O0 to -O2 in the target settings, and I had similar results for nearly all of them.

----

Hey that long assingment is pretty neat. I didn't know you could do that!! I played with this code a little more this afternoon and came to some interesting conclusions. 



*it's way to easy to waste time optimizing code!!!
*bitwise operators can add more overhead than "if" statememts
*touching memory eventually becomes the bottleneck



I agree that gcc is getting pretty darn good. I also think that the GeeFour chip must have some pretty awesome branch predictors, because I noticed that bitwise operators were more of a bottleneck than conditional statements. The fastest converter only used 5 pairs of bitwise operators (& and >>),  five switch statements and one conditional.  I know I could speed things up more if I could reduce the number of times memory gets touched during character assignments (i.e. *pointer=some long value). Since the GeeFour is a 32bit processor, I wonder if you can't touch memory any wider than you can with long assignments. I also wonder if Altivec allows you to touch memory in 128 bit chunks? If that's true than maybe it might be worth learning more about Altivec. --zootbobbalu

----

That form of assignment (unsigned long num = 'ABDC';) is a legacy idiom from Mac OS Classic for representing Type and Creator codes, which I am horribly misusing here.  As for Altivec, I plead insufficient cluefulness; I glanced at the instruction list, and it just made my brain hurt.  -- Bo

