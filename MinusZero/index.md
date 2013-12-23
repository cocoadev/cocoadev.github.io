---
layout: page
title: MinusZero
---

I've got a peculiar string formatting problem -- one which I could certainly fix with a little manual string parsing, but I'm curious if there's a better way; perhaps one to circumvent the issue altogether.

Now, the trouble is in the area with the red circle. I've got some code tracking the position, velocity, and delta-velocity of objects in my simulation and I'm using NSString stringWithFormat to write to an NSTextField this information.

    

-(void) updateListing
{
	if ( currentSimObject >= 0 && currentSimObject < simulation->countSimObjects() )
	{
		std::string name;
		simulation->nameOfSimObject( currentSimObject, name );
		[objectNameTF setStringValue: [NSString stringWithCString: name.c_str()]];

		float x, y, z;
		simulation->positionOfSimObject( currentSimObject, x, y, z );
		[objectPositionTF setStringValue: [NSString stringWithFormat: 
			@"Position: ( %.2f, %.2f, %.2f )", x, y, z ]];

		simulation->linearVelocityOfSimObject( currentSimObject, x, y, z );
		[objectVelocityTF setStringValue: [NSString stringWithFormat: 
			@"Velocity: ( %.2f, %.2f, %.2f ) ddv: ( %.2f, %.2f %.2f )", 
			x, y, z, x - lastDx, y - lastDy, z - lastDz ]];
			
		lastDx = x;
		lastDy = y;
		lastDz = z;
	}
	else
	{
		[objectNameTF setStringValue: @"No object selected"];
		[objectPositionTF setStringValue: @"Position:"];
		[objectVelocityTF setStringValue: @"Velocity:"];
	}
}



The problem is that when the value is zero, it sometimes writes in a negative zero, and sometimes plain-old zero. In fact, watching it run, it toggles between the two for the same field while running. It's very peculiar.

I could of course just test for zero and manually insert zero, but that seems like overkill. Have I missed some aspect of NSString's format string? 

--ShamylZakariya

----
Don't know, but that's an awesome pict, especially if its getting rendered 30fps :)

It's keyed to get 20fps, since that's the safest & slowest speed the physics engine can integrate without too many interpenetration errors. 20's also easy on the eyes, and best of all, at 20 the graphics take up less than 5% cpu on my little PB12, even with stencil shadows, antialiasing & edge-tracing. --ShamylZakariya

----
The problem is that due to the limited precision of floats your value will sometimes be -0.000001 or similar, which will show up as "-0.0".


Agreed. Try rounding the floats to the same precision as your format string before printing them. Alternately, use integers only in your program and scale by 100.

----

Yes -- you're correct. I feel like king doofus now. 

It's funny how the simplest things can throw you when you're spread too thin. Here I'm developing, simultaneously, a "brain", a simulation environment for the robot it controls, device drivers for talking to real control electronics and a *real* robot which will ultimately run the brain code locally ( including custom electronics for measuring torque feedback on motors, etc ) -- and I miss out on something I would have caught when I was 15 writing pascal on a mac classic. 

Well, I consider that proof that I should take a rest now and then.

--ShamylZakariya

