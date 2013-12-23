---
layout: page
title: HowToUseVariables
---

Understanding variables is essential to C as well as objective-C.  They are used the same in each language.

A variable is a word that refers to something such as a number or a word.

 C includes the following fundamental data types: 
----
    
   type                      use            size            range

----
    
   char                    character       8 bits       -128 to 127
   unsigned char           character       8 bits       0 to 255
   short                   integer         16 bits      -32,768 to 32,767
   unsigned short          integer         16 bits      0 to 65,535
   int                     integer         32 bits      -32,768 to 32,767
   unsigned int            integer         32 bits      0 to 65,535
   long                    integer         32 bits      -2,147,483,648 to 2,147,483,647
   unsigned long           integer         32 bits      0 to 4,294,967,295
   float                   real            32 bits      1.2E-38 to 3.4E+38
   double                  real            64 bits      2.2E-308 to 1.8E+308
   long double             real            128 bits     3.4E-4932 to 1.2E+4932

(Be aware that these may change in the future, for example on the 64-bit PowerPC in the GeeFive. Rest assured, they'll only get bigger.)
----

When referring to variables in certain functions you can use the following codes (Objective-C adds the last one):
     
* %d for integer         
* %c for a single character         
* %F for a double number          
* %s for a string this reads in a single word
* %@ for an object (including NSStrings)


See also HowToUsePointers

Go back to HowToProgramInOSX

