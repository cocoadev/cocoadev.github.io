---
layout: page
title: ScriptingLanguage
---

A ScriptingLanguage is the counterpart of a compiled language. ScriptingLanguages are interpreted, whilst compiled languages are, well, compiled, turned into machine code.

Examples of ScriptingLanguages: PerlLang, PythonLang, RubyLang, PHPLang, JavaScript

Examples of CompiledLanguages: ObjC, JaVA, C/C++

(ignore the "Lang" part, it's just for the WordsJammedTogether syntax)

Oh, and don't forget about AppleScript....   :)

----

Java isn't compiled in quite the same sense that C is, though, since it's compiled to a byte code and then recompiled at execution time. Right?

-- FinlayDobbie


Java is compiled to binary code that a JavaVirtualMachine can run, in the same way that a C program is compiled to binary code that a PowerPC or Intel machine can run. The JavaVM then translates the Java instructions into PPC or x86 instructions (or system calls, dunno). Has anyone tried JavaVM assembler?

-- TheoHultberg

----

Has anyone tried JavaVM assembler?  Not worth the time.  I wouldn't want to write the Java assembly
code required for a simple loop as illustrated in this example from The Java Virtual Machine Specification
[http://java.sun.com/docs/books/vmspec/].  Most of us would get more out of learning more Cocoa.
  -- BruceB

http://java.sun.com/docs/books/vmspec/2nd-edition/html/Compiling.doc.html

The spin method simply spins around an empty for loop 100 times: 

    
void spin()
{ 
    int i; 

    for (i = 0; i < 100; i++) { 
          ;                           // Loop body is empty 
    }
}


A compiler might compile spin to 

    
Method void spin() 

  0   iconst_0      // Push int constant 0
  1   istore_1      // Store into local variable 1 ( i=0)
  2   goto 8        // First time through don't increment 
  5   iinc 1 1      // Increment local variable 1 by 1 ( i++ )
  8   iload_1       // Push local variable 1 ( i)
  9   bipush 100    // Push int constant 100 
 11   if_icmplt 5   // Compare and loop if less than ( i<100 )
 14   return        // Return void when done 


----
Generally, "scripting langauges" have a high instant-gratification factor, and frequently string-oriented (perl, tcl, python, etc).  You write your script, and it runs.  Compiled languages have a compilation and (many times) a linking phase.  Even though Java is interpreted by the JVM, it's considered to be a compiled langauge since it actually gets explicitly compiled, and doesn't have the light-weight, string-oriented flavor to it.

----

Jython - http://www.jython.org is very powerful.  It combines Python (interpreted) with Java's classes.

PyObjC might also be an option.

