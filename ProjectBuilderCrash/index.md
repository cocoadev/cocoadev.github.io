---
layout: page
title: ProjectBuilderCrash
---

Hopefully a very easy question from a fledgling developer...

every time i try to start project builder it bounces twice and crashes with no error.  the crash log says..

Command:    Project Builder
PID:        710

Exception:  EXC_BREAKPOINT (0x0006)
Code[0]:    0x00000001Code[1]:    0x8fe01280

Thread 0 Crashed:
 #0   0x8fe01280 in halt
 #1   0x8fe0329c in load_library_image
 #2   0x8fe06134 in load_images_libraries
 #3   0x8fe05b28 in load_dependent_libraries
 #4   0x8fe026f4 in load_executable_image
 #5   0x8fe01410 in _dyld_init

PPC Thread State:
  srr0: 0x8fe01280 srr1: 0x0002f030                vrsave: 0x00000000
   xer: 0x20000000   lr: 0x8fe0a064  ctr: 0x8fe28f78   mq: 0x00000000
    r0: 0x00000004   r1: 0xbffffb30   r2: 0x2200428c   r3: 0x000000da
    r4: 0x00000000   r5: 0x000000da   r6: 0x0000290a   r7: 0x726e6f20
    r8: 0x66696c65   r9: 0x00000000  r10: 0xbffff8ab  r11: 0x00000026
   r12: 0x8fe71b02  r13: 0x00000000  r14: 0x000042a0  r15: 0x00000000
   r16: 0xbffffe34  r17: 0x00000001  r18: 0x00000000  r19: 0x000038f4
   r20: 0x00000000  r21: 0x00000000  r22: 0x00000000  r23: 0x00000000
   r24: 0x0000f348  r25: 0x00000002  r26: 0x00000002  r27: 0x00000000
   r28: 0x993f7964  r29: 0x00000000  r30: 0x8fe484d8  r31: 0x8fe09ecc


can anybody help?  best thing for me is email timbuk3 @lycos.com (remove the space), but i'll check here too.  thx!
-Jesse

Not sure if this is a reasonable strategy for the situation, but have you tried removing the preferences file? Either delete it or move it elsewhere and, um, be hopeful. -- RobRix

----
Maybe you have the wrong version of Developer Tools installed for the version of OS X you have installed.

