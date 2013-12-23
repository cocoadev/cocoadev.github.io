---
layout: page
title: AssemblerCode
---

Taken from the AtomicThreadSafety page:

I believe assembly code is lame because it makes your code less portable. Mac OS X is interesting because its core operating system layer is cross platform: darwin can compile and run on PowerPC and Intel systems. Not a few years ago, Cocoa (then called YellowBox) was available on Mach for PowerPC, Mach for Intel, and Windows (Intel). While now it's available only for PowerPC, seeing PowerPC-specific coding tips presented as a general purpose Cocoa programming tips seem even more lame. 

I make sure all of my low-level tools (command line tools, library code, etc.) will build on all platforms supported by Apple's compiler (at least in theory). This means, among other things, **no asm**.

I understand that this point of view is not for everyone. Mac OS X's interesting bits, Cocoa included, are all available only for PowerPC only. People are free to use whatever techniques they like to solve whatever problems. But I hope we can limit PowerPC-specific techniques to interesting optimizations and clever hacks in the future.

-- MikeTrent

One can have compile time checks to determine the environment and exchange asm for c code. Do you have the same objections towards use of AltiVec?

-- DavidRemahl

Well, he did say "interesting optimizations"... just the same, I'd rather do the compile-time checks, because we don't have Cocoa on other platforms and it doesn't look (IMHO) like we're going to get it on other platforms. If we do get Cocoa/Wintel, it would make sense to reimplement the AtomicSwap() function then and not worry about it before then. I think code should be kept as portable as possible, but I don't think it makes sense to worry about it for one function. -- RobRix

There are various #defines you can check for at build time. You can fall back to C code if you're not building on the right processor, or you can include asm routines for all processors you're interested in. Your choice. My choice is to never use asm, except when I simply must have it (such as in the case of AltiVec).

I object to AltiVec in general practice since AltiVec code crashes on my G3s! Functions that contain AltiVec instructions (the entire functions) should be isolated by

* ifdef'ing the code with the proper architectural flags (__ppc__ and __VEC__ as appropriate)
* checking for AltiVec at runtime and calling that function only once you're sure a vector unit is present (I use sysctl for this)

Skipping the first item will mean your code won't build if someone tries to compile w/o the AltiVec code generation turned on (no surprise, I guess), even on PowerPC processors. You'll also have trouble building for Intel (still no surprise). Skipping the second item will mean your code will **crash and burn** on G3s.

I have no objection to using AltiVec as an optimization. Check out the Flurry-1.4 screen saver module for a good example of software that will compile on various platforms, will run AltiVec code on GeeFour systems, and run normally on G3 systems. 

-- MikeTrent

Well, personally I view reducing the time spent acquiring a lock by an order of magnitude an interesting optimization. Coding all this on x86 chips is even easier - one asm instruction to swap-and-lock, instead of the several my method above needs - so portability is hardly a major setback.

-- KritTer

----

What do people think? Is assembler acceptable, or should we remove all taint of it from our Cocoa code? If it is acceptable, should we confine its use to small, well-documented functions or can it be used more extensively (still with hefty documentation to aid porting)?

(See CodingAnEfficientProxyObject for a reason one might want heavy assembler-use.)

----

A bit off-topic one could argue that it is not very nice to use asm at all, if we're talking about Cocoa. It's supposed to be object-oriented, and if we want to be pure object-oriented that means that even C-functions are forbidden, along with ints, chars, c-arrays, pointers, structs, you name it. It's possible to get by without all that - but the code is not fast... 

If we're talking efficient code, there is little point in using Cocoa... but the world is not black and white, sometimes (ehm, mostly, actually) one wants to optimize small blocks and not the whole app.

-- TheoHultberg/Iconara


I'll believe your first statement when you show me how to add 2 and 2 using just Cocoa as it currently stands. I'd need to use ints. (I assume by "no pointers", you are not referring to the pointers ObjC has to use to reference objects, but pointer usage that C can understand.)

-- KritTer


Well, pointers would be C-style pointers (char*) and references would be object ObjC-object-references (id or NSString*). They are actually the same thing, but in the context of the languages they are not. Pointers could actually be allowed in an object-oriented environment, but the everything-is-a-reference is much nicer. 

NSNumber is a silly class. Had it defined add, multiply, divide and a few other methods, Cococa could become purely object-oriented. Now one has to resort to ints and doubles, bummer. True, somewhere you'd have to resort to ints anyway, to be able to write the class dealing with arithmetic, but the code you wrote that used that class would be object-oriented, and that's what matters. Object-orientedness is just a giant overhead, anyway.

-- TheoHultberg/Iconara

----
Just out of curiosity does anyone know where I can find a good tutorial or refrence about assembly and os x.

*For assembly on the PowerPC: http://usgibm.nersc.gov/alangref/toc.htm is a good review of all the assembler codes, while http://the.wall.riscom.net/books/proc/ppc/cwg/cwg_toc.html provides a lot of extra information you'll probably need.*
**Is it just me, or do neither of these two links work anymore? Anyone care to try and find these reposted somewhere else? I googled but came up short. --KevinPerry**
*
Also check this:
http://www.lightsoft.co.uk/Fantasm/Beginners/begin1.html
*

----

WRT: "Coding all this on x86 chips is even easier..." So why not include that on your discussion so that Intel darwin-heads can benefit? :-)

-- MikeTrent

Just because I know it's easy doesn't mean I know how to do it. Besides, how could I debug my code? :)

-- KritTer

Now you understand why ASM means "not portable.' -- MikeTrent

----

OK, I can't figure it out.  Could someone provide a simple example on how someone would do something like setting a C variable to a register value inside an asm call?

-- BrianMoore

Off the top of my head,
    
asm volatile ("addi %0,r10,0" : "=r"(c_variable) : : "cr0", "memory");

There are typically no simple instructions for the PowerPC, like move or whatever. Instead, one needs to coopt other functions. In this case, I used addi (add immediate) to sum the contents of register 10 with the 16-bit integer 0, placing the result in c_variable, which should be a 32-bit integer. In other words, place r10 into c_variable. This kind of shenannigans is why we call it a Reduced Instruction-Set Chip.

(Sorry for the delay in replying.)

-- KritTer

Keep in mind you might be better off with C. Remember, C combines all the power and elegance of assembly code with all the readability and portability of assembly code ;) -- RobRix

*"All" I would disagree with. At least as far as "power" goes. -- KritTer*

So would I. It's still funny, though! -- RobRix

In the same vein:

"Java. The blazing speed of Smalltalk.  The elegance and simplicity of C++." 

* "Perl. The incredible speed of Python, the comprehensibility of Microsoft manuals, and the upgrade frequency of Quark." *

Except that perl is actually updated pretty frequently, at least in minor ways. Anyway, the evil equivalent: Objective-C. All the speed and portability of QBASIC, combined with the raw power of Visual Basic.

----

Does anyone know of somewhere I can learn to program ASM for Mac OS X?  The only tutorial that seems to still be live is the Fantasm one, which is not Carbonized.  Any chance of getting some information that might be useful? --OwenAnderson

----

GccThreePointThree can use CodeWarrior style inline assembler blocks, which are much easier to use than older Gcc's undocumented thingies.
-- AlexanderStrange

----

Smashing the Mac For Fun & Profit
 
http://www.securiteam.com/securityreviews/5HP040KB5S.html

----

Hacker's Delight, by Henry S. Warren, Jr., is a marvelous book on numerical algorithms.  The author provides C code for most of them, but indicates how the algorithms can be programmed in a PPC-like assembler.  For example, the three-valued compare function *cmp(x,y)* defined as -1 if *x < y*, 0 if *x = y*, and 1 if *x > y*, applied to unsigned integers, can be done in 4 PPC instructions:

    
  subf  R5,Ry,Rx  # R5 <-- Rx - Ry
  subfc R6,Rx,Ry  # R6 <-- Ry - Rx, set carry
  subfe R7,Ry,Rx  # R7 <-- Rx - Ry + carry, set carry
  subfe R8,R7,R5  # R8 <-- R5 - R7 + carry, (set carry).
                  # Page 20


I don't know if the syntax here translates directly to the PPC.

----

KritTer, your example is wrong.
    
asm volatile ("mr %0,r10" : "=r"(c_variable))

is correct, I think. An addi from r10 to the register with c_variable certainly doesn't touch memory or any condition registers!
-- AlexanderStrange

You could be right. I'm unfamiliar with the "mr" instruction, though - you are sure it's PPC not 68k? (There are so many abbreviated forms for programmer convenience...) -- KritTer

"mr x,y" is shorthand for "ori x, y, 0", if I'm not mistaken. -- ChrisT

----

Alright, if GCC 3.3 supports this snappy CodeWarrior-style inline assembler, where can us mere mortals who don't use CodeWarrior find documentation on it?  I pulled out my old copy of CodeWarrior 4, but wasn't able to find anything in the manuals about inline assembler. --OwenAnderson

"asm volatile" is not snappy CodeWarrior, it's clunky and horrible GCC. Bleuch. I'd far rather CodeWarrior's nice pure-assembler functions any day. <edit> Oh, lost the thread there. Nevva mind. -- KritTer

----

The XCode debugger now supports disassembly. FYI, since I just happened to be on this page.

----

Does anyone have an idea what is wrong with this code?

    
void PutPixel(int x,int y,char color) {
 int offset;
 if (x > 319)
 {
    x = 319;
 }
 
 if (y > 199)
 {
    y = 199;
 }
 if ((x > -1) && (x < 320) && (y > -1) && (y < 200)) {
  offset = (320 * y) + x;   /*  asm           */
  asm ("mov ax,0a000h");
  asm ("mov es,ax");
  asm ("mov di,%[offset]" : [offset] "=X" (offset));
  asm ("mov al,%[color]" : [color] "=X" (color));
  asm ("mov [es:di],al");
}     
}

int main () {
int i = 0;
while (i < 320)
{
    PutPixel(i,i,(char)0001);
    i++;
}
}


Theoretically it draws a line a cross the monitor. No such luck yet, though. (It doesn't even compile, GCC says "invalid mnemonic: 'mov' " )

----

Well, that is x86 asm which bears nearly no resemblance to PPC asm so you will have to re-write every asm line.  Additionally, code such as this assumes that you are working in real-mode on an x86 which has the VGA memory mapped in at segment 0xA000.  This code looks as though it was written to run in DOS only.  If you want to work on OS X, you will need to use the normal graphics API.  You could re-write the PutPixel method you have to just use these calls so you don't have to modify your existing code, if you wanted a quick way through it.

----

Aww crap. Thanks though.

Edit: Actually, I was hoping to be able to draw without an OS installed.

*Quick answer: don't want that :)*

**Much longer answer: figure out OpenFirmware, see if you can get it from in there; if not, figure out how to write a kernel that you can load using one of the many bootloaders, and have *that* draw. Prolly still outta luck.** *I'd love to see how user-friendly the setup of that product turns out to be... ;)*

----

Interesting question: Will the above x86 asm code work on the Mactels? Or are those asm calls DOS-related (I learned some DOS-specific asm a few semesters back, so I'm thinking this may be the case).

*As implied above, the ASM there is made to be run on a x86 system running in real mode, which usually means DOS. Other, more advanced operating systems require you to use their own calls to draw on the screen, so that they can pass the call through their own video card drivers. If you need for some reason to draw straight to the screen, you will have to resort to using direct-to-screen APIs offered by the system, such as DirectX's screen section on Windows (or OpenGL (?) on the Mac?).*

