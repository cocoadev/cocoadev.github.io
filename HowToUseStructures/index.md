---
layout: page
title: HowToUseStructures
---

Structures are very useful.  They allow you to organize your data in one place, and retrieve it in a way that makes sense.

**How to define a structure**

Put this code in your implementation file between your import lines and your @implementation line.  -- I think this is right.  Is it?  What if it is declared in between the braces in the header file, then where do you define it?
    
struct myStruct
{
    short     myInteger;
    long      myLongInteger;
    float     myFloatingPointValue;
};


**How to declare structure**

Put this code wherever you would normally declare a variable.  In other words, put it in a method in your implementation file, or between the braces in your header file.
    
struct myStruct    s;


**How to add values to a structure**
    
s.myInteger = 7;
s.myLongInteger = 437090;
s.myFloatingPointValue = 53.467;


Note : Adding something about how to initialize structures seems appropriate here.

----

If you want to avoid putting "struct" in front of all your struct variables (i.e., "struct myStruct s") you can typedef your struct as something else:

    
struct myStruct
{
    short     myInteger;
    long      myLongInteger;
    float     myFloatingPointValue;
};
typedef struct myStruct myStruct;

struct myStruct gStruct1; // this global variable reference is ok
myStruct gStruct2; // this global variable reference is also ok


This typedef is commonly done on one line:

    
typedef struct {
    short     myInteger;
    long      myLongInteger;
    float     myFloatingPointValue;
} myStruct;


Also, this topic is all covered in Kernighan & Richie's "The C Programming Language", a book every C-based programming-language programmer should memorize.

Also, for the love of Pete, can we StopUsingMyInVariableNames? This probably goes without saying, but it's a very bad habit.

-- MikeTrent

----

Back to HowToProgramInOSX

