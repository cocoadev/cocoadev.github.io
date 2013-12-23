---
layout: page
title: WhatToKnowAboutCFunctions
---

[Topic]

Would anyone like to work on this page?  Add your knowledge?  This page could be miles long, but I'll try to give just the essentials.  Please add whatever you think necessary. 

**C function basics**

Modern C compilers require a function prototype, or declaration, and a function definition.  A prototype
defines the function's return type, name, and argument list.  The definition defines the
behavior of the function.

For those compilers that do require the function prototype, it typically must be declared before it is used. This cannot be done inside other functions or struct/enum/etc declarations.

**Anatomy of a C function**

A typical function prototype, or definition looks like this:

    
<return type> function_name ( <argument list> );


A typical function definition looks like this:

    
<return type> function_name ( <argument list> )
{
    body of function

    [return statement]
}

<var name> :== letter (a-z or A-Z) followed by letters, numbers or underscore (_)
<basic data type> :== char | int | long| short | double | float
<return type> :==  void | <basic data type> | system-defined type | user-defined type
<argument list> :== (nothing) | <return type> [* | **]  <var name> [, <argument list>]



**How to call a C functions and pass it values**

To call a function, or in other words, cause execution of a program to pause on the current line of execution and enter another until it completes, simply use the following pattern: 

    
function_name ( <parameter list> );


The parameter list is a list of 0 or more comma delimited variables. The number and type of each variable should match those in the function prototype. Order is important! If the types do match, then the values will be casted to the correct type. If this isn't possible, you'll get a compiler warning or error.

Be aware that function name alone does not identify a unique function. A function is uniquely identified by its function name and the number of arguments it has. For example, the following two function declarations are not equivalent and have different definitions:

    
void my_function (int my_variable);
void my_function (int my_variable, float also_my_variable);


However, the following will clash and give you a compiler error because it thinks you are redefining the same function because the name and number of variables are the same *(not in C++, General/ObjC ? -- That's true for C++, but believe my point is true for General/ObjC, and C [which is what we're dealing with here].)*:

    
void my_function (int my_variable);
void my_function (float my_variable);

/* --- OR --- */

int my_other_function ();
float my_other_function ();


Of course, if a function has no arguments and the return type is void, it is called by simply appending the parentheses to the function name as follows:

    
function_name ();


**How to return a value from a C function**

If you know how to return a value from an Objective-C method, then you know how to do it in C.

    
<return type> function_name ( <argument list> )
{
        

    return some_value;

    (    )
}


Note that when the return statement is reached in the function, the function will terminate having returned the appropriate value and the program will continue from the point at which the former function was called. Also, the type of the returned value should match the return type in the function declaration. If it does not, the value will be casted to the correct type if it can, otherwise you will receive a compiler warning or error.

The return statement works just a bit a bit differently if the return type of the function is void:

    
void function_name ( <argument list> )
{
        

    return;

    (    )
}


The return statement operates in exactly the same manner as above, but no value is returned to the caller upon termination. If there is no code following the return statement, the return statement may be omitted and the function will terminate upon reaching the end of the block.

If you try to return a value in a void function or try to use the return value of a void function you will receive a compiler warning or error.
 
**How to use the values returned from a C function.**

A function call with a non-void return type can be used in any statement and is treated just like a constant variable of that return type (Note, if you try and change the value using an assignment or increment/decrement operator, you will receive a compiler error).

Here are some examples:

    
int my_variable = my_function();

my_variable += (my_function() % (my_other_function(my_variable) + 10));


**How to modify values in a C function, and have these changes reflected in other functions without returning a value.**

The title of this topic is confusing, but I'll explain in depth here.

When you pass values to a function, you aren't giving the function the actual variable, but in fact, a copy of it. You are free to change these copies as you wish inside the function, provided they aren't declared constant. However, once the function terminates, those values are lost, unless you explicitly use the return statement to return a new value. However, what if you change two values that you want to remember? Here's a classic example:

    
void foo()
{
    int my_int = 3, my_other_int = 4;
    
    printf( "my_int: %3d\t", my_int );   /*  show values before  */
    printf( "my_other_int: %3d\n", my_other_int );

    swap_ints (my_int, my_other_int);

    printf( "my_int: %3d\t", my_int );   /*  show values after  */
    printf( "my_other_int: %3d\n", my_other_int );
}

void swap_ints (int an_int, int another_int)
{
    int tmp_int = an_int;
    an_int = another_int;
    another_int = tmp_int;
}


As expected, at the end of the swap_ints function (before it terminates!) the values in another_int and an_int have switched. However, upon return to foo, my_int is still 3 and my_other_int is still 4! Why? Because we gave swap_ints **copies** of my_int and my_other_int, remember?

To overcome this, we need to do something called "pass by reference" (we were just doing "pass by value"). If this next part is too confusing, go read General/HowToUsePointers and then come back.

The following code will work as expected, switching the values of my_int and my_other_int in foo:

    
void foo()
{
    int my_int = 3, my_other_int = 4;

    printf( "my_int: %3d\t", my_int );   /*  show values before  */
    printf( "my_other_int: %3d\n", my_other_int );

    swap_ints (&my_int, &my_other_int);

    printf( "my_int: %3d\t", my_int );   /*  show values after  */
    printf( "my_other_int: %3d\n", my_other_int );
}

void swap_ints (int *an_int_ptr, int *another_int_ptr)
{
    int tmp_int = *an_int_ptr;
    *an_int_ptr = *another_int_ptr;
    *another_int_ptr = tmp_int;
}


Not a lot different, eh? First, look in foo. When we call swap_ints, we've prepended an ampersand (&) to the variable names. This will give swap_ints (copies of!) my_int's and my_other_int's addresses (pointers) instead of their values. Inside swap_ints we're dereferencing each pointer and changing the value stored at that address. Remember, even though we're given a copy of the address, the changes will still exist in memory after the function completes!

Back to General/HowToProgramInOSX

C Languages Knowledge Base
[http://c.ittoolbox.com/nav/t.asp?t=366&p=366&h1=366]
contains a number of articles and such that may be of interest to developers.

Another introductory article in General/MacDevCenter's new series:
[http://www.macdevcenter.com/pub/a/mac/2003/08/01/cocoa_series.html]
