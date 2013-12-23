---
layout: page
title: GestaltAndCocoa
---

I've recently been fidling with Gestalt.  I'm looking to capture certain information from my user's machine so they can send me proper bug reports on my software.  Gestalt is a Carbon idiom, and has been brought over from the Old Mac days, however it's perfect for what I need.

I'm grabbing the User's OS Version and OS Build information, neither of which require Gestalt.  However, Gestalt comes in when I need to grab my User's RAM levels, their Machine's clock speed, and their processor type.

Using:
    
if (!Gestalt(gestaltPhysicalRAMSize, &gestaltReturnValue))
    {
    NSLog(@"%d MB",(gestaltReturnValue/1048576));
    [advancedRAMLevel setStringValue:[NSString
        stringWithFormat:@"%d MB",
                         (gestaltReturnValue/1048576)]]; 
    }


I was able to grab the user's RAM levels.  However the Gestalt documentation is not very precise in how it should be implemented for the Processor speed, or the Processor Type. Does anyone know how I might go about implementing this on these two items?  They're the last things I need from this application.

ChrisGiddings


chris.giddings AT ripplesw.net
[updated email address

----

Try the following:

    
	{
		OSType		returnType;
		long		gestaltReturnValue,
			swappedReturnValue;
		
		NSLog(@"Gestalt fun...");
		
		returnType=Gestalt(gestaltPhysicalRAMSize, &gestaltReturnValue);
		if (!returnType)
		{
			NSLog(@"RAM: %d MB",(gestaltReturnValue/1048576));
		} else {
			NSLog(@"error calling Gestalt: %d", returnType);
		}
		
		returnType=Gestalt(gestaltNativeCPUtype, &gestaltReturnValue);
		if (!returnType)
		{
			char		type[5] = { 0 };
			swappedReturnValue = EndianU32_BtoN(gestaltReturnValue);
			memmove( type, &swappedReturnValue, 4 );
			NSLog(@"NativeCPUType: '%s' (%d)",type,gestaltReturnValue);
			
			switch(gestaltReturnValue) {
				case gestaltCPU601:        NSLog(@"PowerPC 601"); break;
				case gestaltCPU603:        NSLog(@"PowerPC 603"); break;
				case gestaltCPU603e:       NSLog(@"PowerPC 603e"); break;
				case gestaltCPU603ev:      NSLog(@"PowerPC 603ev"); break;
				case gestaltCPU604:        NSLog(@"PowerPC 604"); break;
				case gestaltCPU604e:       NSLog(@"PowerPC 604e"); break;
				case gestaltCPU604ev:      NSLog(@"PowerPC 604ev"); break;
				case gestaltCPU750:        NSLog(@"G3"); break;
				case gestaltCPUG4:         NSLog(@"G4"); break;
				case gestaltCPU970:        NSLog(@"G5 (970)"); break;
				case gestaltCPU970FX:      NSLog(@"G5 (970 FX)"); break;
				case gestaltCPU486 :       NSLog(@"Intel 486"); break;
				case gestaltCPUPentium:    NSLog(@"Intel Pentium"); break;
				case gestaltCPUPentiumPro: NSLog(@"Intel Pentium Pro"); break;
				case gestaltCPUPentiumII:  NSLog(@"Intel Pentium II"); break;
				case gestaltCPUX86:        NSLog(@"Intel x86"); break;
				case gestaltCPUPentium4:   NSLog(@"Intel Pentium 4"); break;
				default: NSLog(@"error calling Gestalt: %d", returnType);
			}
		}
		
		returnType=Gestalt(gestaltProcClkSpeed, &gestaltReturnValue);
		if (!returnType)
		{
			NSLog(@"procSpeed: %d MHz",(gestaltReturnValue/1000000));
		} else {
			NSLog(@"error calling Gestalt: %d", returnType);
		}
		
		returnType=Gestalt( gestaltPowerPCProcessorFeatures, &gestaltReturnValue);
		if (!returnType)
		{
			NSLog(@"PowerPC ProcFeatures: %d",(gestaltReturnValue));
			if (gestaltPowerPCHasDCBAInstruction==gestaltReturnValue) {
			}
		} else {
			NSLog(@"error calling Gestalt: %d", returnType);
		}
	}



I get the following on my PowerBook G3:
    
2002-11-18 13:06:03.067 RuleBox[1151] RAM: 640 MB
2002-11-18 13:06:03.067 RuleBox[1151] NativeCPUType: 264
2002-11-18 13:06:03.067 RuleBox[1151] G3 750
2002-11-18 13:06:03.153 RuleBox[1151] procSpeed: 500 MHz
2002-11-18 13:06:03.154 RuleBox[1151] PowerPC ProcFeatures: 3


and this result on my yikes! G4
    
2002-11-18 12:59:57.472 RuleBox[516] RAM: 640 MB
2002-11-18 12:59:57.473 RuleBox[516] NativeCPUType: 268
2002-11-18 12:59:57.473 RuleBox[516] G4
2002-11-18 12:59:57.475 RuleBox[516] procSpeed: 398 MHz
2002-11-18 12:59:57.476 RuleBox[516] PowerPC ProcFeatures: 63


says DiggoryLaycock
----

Thanks DiggoryLaycock,

That helped in every area that I needed.

ChrisGiddings

----

I get the following on my Mac Mini Intel Core Duo:
    
2006-04-18 13:58:05.274 GestaltFun[604] Gestalt fun...
2006-04-18 13:58:05.275 GestaltFun[604] RAM: 1024 MB
2006-04-18 13:58:05.275 GestaltFun[604] NativeCPUType: 'i586' (1765095478)
2006-04-18 13:58:05.275 GestaltFun[604] Intel Pentium
2006-04-18 13:58:05.275 GestaltFun[604] procSpeed: 1660 MHz
2006-04-18 13:58:05.276 GestaltFun[604] error calling Gestalt: -5551


The error (-5551 = gestaltUndefSelectorErr) is to be expected, as an Intel CPU obviously won't have PPC features.

BTW -- I fixed the error statements to use %d instead of %p, turned all those ifs into else-ifs and made it output the type as the FourCC it is, not as an int. I also made it more Intel friendly and added some missing constants from the headers. The Gestalt return value is an OSErr (possible values defined mostly in MacErrors.h) and that's essentially an int. If others with Intel Macs could chime in and let us know what they get ... Since no Pentium Mac ever shipped, I'm tempted to rename "Intel Pentium" to "Core Duo" or just "Intel Core"... can anyone confirm this would be correct?

Also, I think the CPUs up to the 604 were just "PowerPC 604" and the likes, not "G3 604"... didn't the first G3 ship with a 750? I have a Performa 5300 with a 603something here that definitely wasn't labeled "G3", at least. Not that it would matter much, as no CPU earlier than a G3 officially supports OS X, so we could probably get rid of those constants. If anyone does that, feel free to make this a switch().

-- UliKusterer

----

Take a look at libkern/sysctl.h which defines hw.optional.xyz that can be used to determine optional processor features instead.

-- Chris

----

Wikipedia (http://en.wikipedia.org/wiki/PowerPC_G3) supports my claim that the 7x0 CPUs were the first G3s, so I'm changing those.

-- UliKusterer

----

Apple has stated in the Universal Porting Guide that all Intel chips will return a generic value (gestaltCPUPentium, I believe) for the gestaltNativeCPUtype selector. -JonathanGrynspan

----

Also, these days you should use gestaltPhysicalRAMSizeInMegabytes not gestaltPhysicalRAMSize (to support systems with more than 2 GiB of RAM). -- smcbride

----

If you need to get this kind of information programatically you're much better off using the system_profiler tool w/ NSTask.  Let it do the heavy lifting for you.

system_profiler -xml -detailLevel mini

This returns a Plist which you can then turn into objects when you need to with NSPropertyListSerialization.

I replaced the long if/else if chain with a switch anyway.  It needed to be done!

-Harv

