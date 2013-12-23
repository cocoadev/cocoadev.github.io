---
layout: page
title: CurrentSyntaxAndRequests
---

OpenGLScript

Here's the current syntax. Each entry represents the syntax involved for a particular glCall. The shortHand is to help you write OpenGL code faster (remember this project will eventually allow you to export to C source). You can place requests here if you want a function added to this interpreter. You cannot simple add a dictionary entry for a new command because a switch statement is involved for the execution of the compiled code (not exactly inline, but pretty close). Compiled scripts are linked stuctures that provide an enumeration for a "while" loop. Links in the chain provide an integer (the command number) for the switch statement. Each link also provides a pointer to the next link. No message calls are made during the execution of the interpretation. The only overhead involved is a switch statement, the while loop and the time required to inject parameter values from links into gl function calls (only pointer operations are involved in the injection process). The switch statement essentially acts as a function lookup table, but some form of dynamic binding is needed because many glCalls do not except argument vectors (as far as I know). 
----
Currently I am adding facilities to include the use of variables. This will include a math engine that will calculate values based on variables and math operations (i.e. trig functions, addition, multiplication and powers). 

If someone has "Building Cocoa Applications : A Step by Step Guide" that would be great if you could help with the math engine by providing a wrapper for the calculator expample in the book. I think it involved an NSTask and command line tool. The wrapper doesn't need an interface, only a method that excepts a formula string and returns a value. Once the wrapper is submitted, I can finish the interface part by providing the code that converts a formula with variables into a formula with just numbers (unless the example allows variables, I can't remember). In the mean time I am going to write a simple math engine that only uses some of the basics included in <math.h>

----

    
  NSDictionary *entry;
  NSMutableArray *scriptSyntax;

entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"EOF", @"shortHand", 
            @"EndOfFile", @"glCall",		// 0 - end of file or script in this case
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"reset", @"shortHand", 
            @"glLoadIdentity", @"glCall",		// 1 - reset the current modelview matrix
            @"%@glLoadIdentity();\n", @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"begin", @"shortHand", 
            @"glBegin", @"glCall",		// 2 - begin
            @"%@glBegin(%@);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"disable", @"shortHand", 
            @"glDisable", @"glCall",		// 3 - disable
            @"%@glDisable(%@);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"end", @"shortHand", 
            @"glEnd", @"glCall",			// 4 - to declare the end of a triangle or quadrangle
            @"%@glEnd();\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"tex", @"shortHand", 
            @"glBindTexture", @"glCall",		// 5 - selects texture
            @"%@glBindTexture(%@, %i);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"texcord", @"shortHand", 
            @"glTexCoord2f", @"glCall",		// 6 - sets texture cordinates
            @"%@glTexCoord2f(%[float], %[float]);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"norm", @"shortHand", 
            @"glNormal3f", @"glCall",		// 7 - sets normal pointing toward viewer
            @"%@glNormal3f(%[float], %[float], %[float]);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"light", @"shortHand", 
            @"glLightfv", @"glCall",		// 8 - sets lighting (only responds to light1 so far)
            @"%@glLightfv(%@, %@, );\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"enable", @"shortHand", 
            @"glEnable", @"glCall", 		// 9 - enable
            @"%@glEnable(%@);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"rgb", @"shortHand", 
            @"glColor3f", @"glCall",		// 10 - sets rgb color
            @"%@glColor3f(%[float], %[float], %[float]);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"vert", @"shortHand", 
            @"glVertex3f", @"glCall", 		// 11 - vertex
            @"%@glVertex3f(%[float], %[float], %[float]);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"trans", @"shortHand", 
            @"glTranslatef", @"glCall", 		// 12 - translate
            @"%@glTranslatef(%[float], %[float], %[float]);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];
    entry=[NSDictionary dictionaryWithObjectsAndKeys:
            @"rot", @"shortHand", 
            @"glRotatef", @"glCall", 		// 13 - rotate
            @"%@glRotatef(%[float], %[float], %[float], %[float]);\n",  @"format", 
            nil];
    [scriptSyntax addObject:entry];



