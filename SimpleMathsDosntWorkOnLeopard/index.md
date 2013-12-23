---
layout: page
title: SimpleMathsDosntWorkOnLeopard
---

Here's a weird one. I have code that has worked perfectly from time immemorial:

    

NSPoint  p;

p.x = r * sin([self angle]);
p.y = r * cos([self angle]);



This works fine when built against the 10.4.u sdk, but not the 10.5 sdk.     -angle is just a method that returns a float value in radians, r is another float variable. While it compiles without complaint, at runtime the result of the arithmetic is set either to a NaN value or to some very very small value (like 4 x 10-15). Neither are correct. I'm assuming that it's something to do with 64-bit settings but not sure where I should look to check this. The settings are the default set when creating a brand new Cocoa non-document application in Xcode 3. Any pointers? --GC

----

You said 'float', thus maybe it's being extra fussy and you need to use cosf(), sinf()?

----
I suggest PostYourCode, more than what's here, because that should work just fine. Do you get any warnings when compiling?

----
No warnings or anything at compile time. Only wrong values to go on at run time - up to the lines given above, all values look fine in the debugger, and [self angle] returns a sensible value. At least as far as the debugger can see, it is the return values from cos and sin that are wrong. There doesn't seem to be much to be gained from posting the full code, which has worked from 10.2 through 10.4 without a problem. It's part of my public code for GCGradient if you really want to look at it in detail, but because of this problem that class isn't working when compiled against the 10.5 sdk. --GC

----

Have you *tried* the cosf() and sinf() suggestions? The changes to the OS to handle 64-bit as well as architecture-safe types would certainly indicate some things (related to primitives) have changed. Since cosf() and sinf() are for floats (whereas cos() and sin() are not), perhaps there's something *to* the advice above ...

----

If you relax IEEE floating-point restrictions when compiling your code, funny things happen. Make sure the restrictions aren't relaxed.

----

OK, I've been hammering on this one. I Tried cosf, sinf, no luck. However, further poking shows that it's not these specific calls that cause the problem, it's much, much weirder! Here's my code:

    

- (void)				fillPath:(NSBezierPath*) path centreOffset:(NSPoint) co
{
	NSRect pb = [path bounds];
	
	// calculate endpoints to take into account the set angle
	
	float		r1, r2, a;
	NSPoint	        sp, ep;
	
	ep.x = NSMidX( pb );
	ep.y = NSMidY( pb );
	
	r1 = pb.size.width * 0.5f;
	r2 = pb.size.height * 0.5f;
	
	if ([self gradientType] == kGCGradientTypeLinear )
	{
		a = [self angle];    //<----- gets a bad assignment (NAN)
		a = [self angle];    //<----- works correctly

		sp.x = ep.x - (r1 * cosf(a));
		sp.y = ep.y - (r2 * sinf(a));
		ep.x = ep.x + (r1 * cosf(a));
		ep.y = ep.y + (r2 * sinf(a));
	}

//... more follows but not relevant here

}



You'll notice that in this version of the code, I call [self angle] twice. On the first call, I get -nan(0x400000), on the second call, I get -1.5707936 which is the correct value. From then on it all works fine. All [self angle] does is to return a data member which I can see in the debugger is correctly valued with -1.5707... So somehow whichever call is made at that point in the code simply gets a bad assignment. If the code is refactored as it was originally so that the first call is to cos([self angle]) then that gets the NAN value which once is there propagates through the remainder of the maths. The double assignment of 'a' does fix the problem as such - the whole class then works correctly, but I'm really mystified as to why this is happening. Looking at the build settings in more detail, the 64-bit thing is a red herring, because the default settings for a new project only enable 32-bit anyway. By the way, regarding: "If you relax IEEE floating-point restrictions when compiling your code, funny things happen. Make sure the restrictions aren't relaxed" what does that mean? I'm not knowingly relaxing anything!

Also, I tried directly assigning the data member to 'a'. That assigns the correct value, but then I get a NAN from the first cosf() function. It seems that it's the function call at this point that causes the problem. I also tried turning off accelerated Obj-C dispatch in case that might have something to do with it but it didn't make any difference. --GC

----

By chance, is your -angle method declared in your interface?

----

Yep.

----

It's been days and nothing has been found. Clearly there's a problem with your code (I'm sure at some point in history, someone's assigned a float retrieved from another instance method, after all). A complete code listing of all relevant and interdependent code is necessary.

