---
layout: page
title: SoundWoes
---

I have created a program that uses sound, After analyzing it with the MallocDebug program it seems I'm getting memory leaks from these sounds.

Can someone tell me if the following code would be required to be freed from memory, and if so, how would it be done?

NSSound *tink = [NSSound soundNamed:@"Tink.aiff"];
        [tink play];

Thanks

----

I created a very simple application consisting of only a timer and the tink sound file. I set it up to play the tink sound once per second and booted it through the Malloc Debug application. After about 2 hours, memory usage was up to about 600 MB.  Leakage related to the NSSound class has caused some customers of mine to experience kernel panics within a couple minutes of starting my program. All who have experienced problems are running g4's with 10.3.3. I also have customers running g4s with 10.3.3 without any problem. I have sent a bug report to apple, but because my powerbook is somewhat tolerant of this, I was unable to send a crash log or a kernel panic log. If someone out there is running a g4 with 10.3.3 and can produce this problem, please file a bug report to apple with crash and kernel panic logs. thanks!!

----
I haven't tested your setup, but my guess is that your app will not unload a resource once it has been loaded.  Provided you don't get additional memory consumption every time you call      NSSound *tink = [NSSound soundNamed:@"Tink.aiff"];
        [tink play];
  you should be fine.  

----

You should not call     release since you did not allocate the object! Interestingly:

    
// main.mm
#import <AppKit/AppKit.h>

int main ()
{
   [NSAutoreleasePool new];
   for(size_t i = 0; i < 5; i++)
      NSLog(@"%p", [NSSound soundNamed:@"Short Whistle"]);
   return 0;
}

Outputs:
    
[3:52][/tmp]> g++ main.mm -framework AppKit
[3:52][/tmp]> ./a.out
2004-03-23 03:52:26.749 a.out[616] 0x529540
2004-03-23 03:52:26.752 a.out[616] 0x529540
2004-03-23 03:52:26.753 a.out[616] 0x529540
2004-03-23 03:52:26.755 a.out[616] 0x529540
2004-03-23 03:52:26.756 a.out[616] 0x529540

So the system is caching the object, and if memory goes up on each call then I think you have stumbled upon a bug in the framework.

----

Yes, you may call it caching. NSSound class maintains a list of loaded sounds, I suppose, not to load one sound twice. Its public interface, however, lacks a means to unload its instances. Indeed, you should not send     release to NSSound objects because from my personal experience they are released and deallocated automatically in Panther. I just could not figure out when. --KonstantinAnoshkin.

----

Yes, you may call it *personal experience* ;) but never ever send     release to objects which you did not allocate yourself, either by sending     alloc to the class object or     copy to an instance of the class.

With regard to caching/unloading, if it really bothers you then you may try to use either     initWithContentsOfFile:byReference: or     initWithData: -- at least the last method should not give rise to any caching, and since you'll     alloc/    init the object yourself, then it is also you who should     releease it.
----

Note to readers:

It should be noted that when this problem was occurring, I was using  a copy of the tink file that I had placed in my applications folder along with my custom classes. I have since modified the program so it references the tink file in the system/library/sounds folder and the memory leakage problem has disappeared. The bottom line is that when using the play method on the soundNamed selector memory leakage will occur if you are using custom sounds. As for memory leakage not causing kernel panics, I have my doubts as the kernel panics are no longer an issue since I modified the code and stopped the leaks. It should be noted that my program was making lots of calls to the play method, so maybe pouring would be a more appropriate term than leaking.

----

A kernel panic would not be caused by a memory leak.  Something else would be going on if you are seeing a kernel panic.  Lots of apps leak memory but nothing in user space should ever be able to cause a panic.  Perhaps the sound use is causing some strange inconsistency within the kernel's sound sub-system on that hardware.

The memory leak is a problem which Apple has to resolve because it is in their code and not yours.  The kernel panic, however, is something quite unrelated.

