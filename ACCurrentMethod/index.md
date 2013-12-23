---
layout: page
title: ACCurrentMethod
---


Note that this makes a good study of AspectCocoa for those interested, but the information that this lets you access is already given to you (I just didn't know it)... the current selector is _cmd, the current target is self, the class that implements the method called is easily discovered by other means, as is the NSMethodSignature.... If this is loaded across the board for every method of every class one could extend this to discover the caller of the method, but that isn't what I needed it for when I made it (see below).   

Note I repeat again, THE CURRENT SELECTOR IS _cmd... it is in the documentation for ObjC language, just look carefully.

----

This is a way to get the information about the currently executing method. (I wanted a way to determine the selector... this should remind you of features of other languages like ECMAScript's Function class)

There is a single currentMethod object which you get by calling [ACCurrentMethod currentMethod],  It allows you access to a subset of the methods available in NSInvocation (no setters, that seems strange to me)

usage:
	(1) you need to include the AspectCocoa framework in your project.
	(2) in the main routine you need to load the aspect for the classes you want to use it with
		[ACCurrentMethod loadForAllClasses]; // other variants exist
	(3) in your methods use it for getting information regarding the current function
		[[ACCurrentMethod currentMethod] selector];

----

why?  glad you asked... I am doing some runtime hacking and want to create many methods that share the same IMP but have different SEL's... see FSObject 

----

    
//ACCurrentMethod.h

#import <Cocoa/Cocoa.h>
#import <AspectCocoa/AspectCocoa.h>

@interface ACCurrentMethod : NSObject
{
	NSArray* myMethodStack;
}
+(void)loadAspectForClassNamed:(NSString*)aClass;
+(void)loadAspectForClass:(ACClass*)aClass;
+(void)loadAspectForAllClasses;
+(void)loadAspectForDefaultClasses;
+(ACCurrentMethod*)currentMethod;
-(void)before:(ACInvocation*)invocation;
-(void)after:(ACInvocation*)invocation;
@end

@interface ACCurrentMethod (CurrentMethodInformation)
-(NSMethodSignature*)methodSignature;
-(SEL)selector;
-(id)target; // when you call this it should return self...
-(Class)getClass; // returns the class that the method is defined in, not [self class]
@end

//ACCurrentMethod.m

#import "ACCurrentMethod.h"

static ACCurrentMethod* gTheACCurrentMethod;

@implementation ACCurrentMethod
+(void)loadAspectForClassNamed:(NSString*)aClass
{
	id allClasses = [ACPointCut enumerateClassesNamed:aClass, nil];
	id eachClass = [allClasses nextObject];
	[self loadAspectForClass:eachClass];
	return;
}
+(void)loadAspectForAllClasses
{
	id allClasses = [ACPointCut enumerateAllClasses];
	id eachClass;
	while(eachClass = [allClasses nextObject]) {
		[self loadAspectForClass:eachClass];
	}
	return;
}
+(void)loadAspectForDefaultClasses
{
	id allClasses = [ACPointCut enumerateDefaultClasses];
	id eachClass;
	while(eachClass = [allClasses nextObject]) {
		[self loadAspectForClass:eachClass];
	}
	return;
}
+(void)loadAspectForClass:(ACClass*)aClass
{
        id allMethods = [aClass methodEnumerator];
        id eachMethod;
        while(eachMethod = [allMethods nextObject]) {
        	if( ! eachMethod methodName] hasPrefix:@"init"] && 
                    ! [[eachMethod methodName] hasPrefix:@"alloc"] &&
                {
                	[aClass addMethod: eachMethod];
                }
        }
        id pointCut = [[[[ACPointCut alloc] initWithJoinPoints:[NSArray arrayWithObject:aClass]];
        ACAspect* currentMethodAspect = [[ACAspect alloc] 
                         initWithPointCut: pointCut  andAdviceObject: [ACCurrentMethod currentMethod]];
        [currentMethodAspect load];
        [currentMethodAspect autorelease];

}
+(ACCurrentMethod*)currentMethod
{
	if(!gTheACCurrentMethod) {
		gTheACCCurrentMethod = [[ACCurrentMethod alloc] init];
	}
	return gTheACCurrentMethod;	
}
-(id)init
{
	[super init];
	if(!myMethodStack) {
		myMethodStack = [[NSMutableArray alloc] init];
		[myMethodStack retain];
	}
	return self;
}
-(void)dealloc
{
	[myMethodStack release];
	[super dealloc];
	return;
}
-(void)before:(ACInvocation*)invocation
{
	[myMethodStack addObject:invocation];
}
-(void)after:(ACInvocation*)invocation
{
	[myMethodStack removeLastObject];
}

@end

@interface ACCurrentMethod (CurrentMethodInformation)
-(NSMethodSignature*)methodSignature
{
	return [[myMethodStack lastObject] methodSignature];
}
-(SEL)selector
{
	return [[myMethodStack lastObject] selector];
}
-(id)target
{
	// when you call this it should return self...
	return [[myMethodStack lastObject] target];
}
-(Class)getClass
{
	// returns the class that the method is defined in, not [self class]
	return [[myMethodStack lastObject] getClass];
}
@end

