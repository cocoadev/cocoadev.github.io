---
layout: page
title: ACCodeGenerator
---

AspectCocoa actually employs two different mechanisms under the hood for intercepting method calls.  One mechanism is slower and less reliable, but the faster one sometimes requires that sometimes code be generated.  This code can be generated based on a pointcut as follows:
    
[ACCodeGenerator generateCodeForPointCut: somePointcut writeTo: @"someoutputfilename"];

The AspectCocoa framework comes with code already generated for almost all of foundation and appkit, which means that in most cases (even when working with your own classes) AspectCocoa will use the already generated code, and you will automatically reap the benefits.

Code generation is used to create a 'replacement IMP' for some method signature.  Once such a 'replacement IMP' has been generated and exists in the runtime, AspectCocoa will be able to use it for all methods of that signature.

In order to know how methods are being wrapped, and when code generation might be usefull, you can use ACAspectManager to enable/disable logging.

----

    
 /* 
     Returns the shared instance of ACCodeGenerator.
     There is a single generator because we never want to generate code 
     for the same signature more than once
 */
 +(ACCodeGenerator *)generator;
 
 /* 
     Returns whether or not code generation is enabled.
 */
 -(BOOL)generateCode;
 
 /* 
     Enables code generation for loading of all future aspects
 */
 -(void)enableCodeGeneration;
 
 /* 
     Disables all code generation.
 */
 -(void)disableCodeGeneration;
 
 /* 
     Output all generated code to the given path
 */
 -(void)writeCodeTo:(NSString *)path;
 
 /* 
     Generates all code needed for a specific pointCut and writes it to a given path
     (if all necessary code already exists in the runtime, an empty file will be generated)
 */
 +(void)generateCodeForPointCut: (ACPointCut *)pointCut writeTo: (NSString *) destination;
 -(void)generateCodeForPointCut: (ACPointCut *)pointCut writeTo: (NSString *) destination;

