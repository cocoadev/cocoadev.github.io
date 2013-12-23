---
layout: page
title: QuickestWayToZeroOutBuffer
---

Does anyone know the quickest way to zero out a large buffer (1MB+). 

The following code takes about 4 milliseconds to zero out 1MB on a 450MHz GeeFour.

    
id buffer=[NSMutableData dataWithLength:262144*4];
int *p;  // <<--- this should be int * d; right ?  oops typo!!
p=[buffer bytes];

for (i=0;i<262144;i+=16) {
    p[i+0]=0;p[i+1]=0;p[i+2]=0;p[i+3]=0;p[i+4]=0;p[i+5]=0;p[i+6]=0;p[i+7]=0;p[i+8]=0;
    p[i+9]=0;p[i+10]=0;p[i+11]=0;p[i+12]=0;p[i+13]=0;p[i+14]=0;p[i+15]=0;
}


this is a little faster, about 2.5 milliseconds

    
id buffer=[NSMutableData dataWithLength:262144*4];
double *d;
d=[buffer bytes];

for (i=0;i<131072;i+=16) {
    d[i+0]=0;d[i+1]=0;d[i+2]=0;d[i+3]=0;d[i+4]=0;d[i+5]=0;d[i+6]=0;d[i+7]=0;d[i+8]=0;
    d[i+9]=0;d[i+10]=0;d[i+11]=0;d[i+12]=0;d[i+13]=0;d[i+14]=0;d[i+15]=0;
}


Seems like there should be some sys call that can do this for you much faster. Anyone know of one?

BTW, I know that NSMutableData provides you with a buffer filled with zeros, but this buffer is goin to be reused a bunch.

----

just found resetBytesInRange!!

this method call takes 1.5 milliseconds, so I guess using doubles wasn't that bad. I guess that makes sense since my memory is 128 bits wide and a double is 64 bits wide. I wonder if I get the same results if I use a data type that is 128 bits wide. But I don't care now, I'm sure 1.5 is as fast as it's going to get. 

*For comparison, you could try using     string.h's     memset, which should be about as fast as possible.*

----

memset is right at 1.5 also, but since method calls take on the order of microseconds I don't think it makes a difference which one to use.

----

Yes, memset is the way to go on Mac OS X. I have been told memset (like all mem* functions) has both been optimized for various architectures, and the compiler can optimize the code around the callsite. (I'll leave the details to a Darwin expert) I'll bet you'll find NSMutableData is actually calling memset on your behalf, which is why performance is the same ;-) -- MikeTrent

