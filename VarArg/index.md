---
layout: page
title: VarArg
---

General/VarArg refers to the capacity in a method or function for it to take a variable number of arguments above a fixed minimum. You declare a function with a variable number of arguments (var args) like this:

    void myFunc(int a, ...);

and a vararg method like this:

    -(void)myMethod:(int)a, ...;

The ellipsis is three periods in a row, and is how the compiler knows that this function/method can take more arguments after the explicitly specified ones.

An example of a vararg function is General/NSLog, which lets you pass a variable number of arguments after the initial General/NSString.

See also: General/UsingVariableNumbersOfArguments, General/VarArgsInObjCMethods, General/VariableLengthArgumentLists.
