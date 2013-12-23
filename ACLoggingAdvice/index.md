---
layout: page
title: ACLoggingAdvice
---

**ACLoggingAdvice** - an example AdviceObject related to AspectCocoa

ACLoggingAdvice is a simple class intended for use as an AdviceObject.  The result of applying an instance of this class as the AdviceObject to a given PointCut (via the loading of an Aspect), will be that whenever any method within the scope of that PointCut gets called, we will NSLog before it gets called, and after it has returned.



**ACLoggingAdvice.h**
    
 #import <Foundation/Foundation.h>
 #import "ACInvocation.h"
 
 @interface ACLoggingAdvice : NSObject {
     NSString* indent;
 
 }
 
 - (id)init;
 
 -(void)before:(ACInvocation *)invocation;
 
 -(void)after:(ACInvocation  *)invocation;
 
 @end


**ACLoggingAdvice.m**
    
 #import "ACLoggingAdvice.h"
 #import </usr/include/objc/objc-class.h>
 
 @implementation ACLoggingAdvice
 
 - (id)init{
     self = [super init];
     if (self){
         indent = [@"" retain];
     }
     return self;
 }
 
 -(void)before:(ACInvocation *)invocation{
     NSLog(@"%@before %s on %s",indent, [invocation selector], [invocation getClass]->name);
     NSString * newIndent = [NSString stringWithFormat: @"-%@",indent];
     [indent release];
     indent = [newIndent retain];
 
 }
 
 -(void)after:(ACInvocation  *)invocation{
     NSString * newIndent = [indent substringFromIndex: 1];
     [indent release];
     indent = [newIndent retain];
     NSLog(@"%@after %s on %s",indent, [invocation selector], [invocation getClass]->name);
 }
 
 @end

