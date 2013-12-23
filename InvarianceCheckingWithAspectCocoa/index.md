---
layout: page
title: InvarianceCheckingWithAspectCocoa
---

Related Links: AspectCocoa, AspectCocoaDocumentation, AdviceProtection, AspectCocoaBugs, ProgrammingByContract, EiffelProgrammingLanguage

----

Invariance checking is defining a set of conditions that must be true for a class... we check this by implementing -invariant and +invariant for any given class, and loading the class in main before we run the application.  Here's what you would do to use this in your project:

    
//main.m
#import <Cocoa/Cocoa.h>
#import "ACInvariance.h"

int main(int argc, const char *argv[])
{
	NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
	
	// change InvariantObject to the name of the class you want to have invariant checking
	// call it multiple times to load for multiple classes
	// InvariantObject should have a -invariant method and a +invariant method defined
	[ACInvariance loadAspectForClass:@"InvariantObject"];
	
        int result = NSApplicationMain(argc, argv);
	
	[pool release];
	
	return result;
}


----
Here's the class itself
    
//ACInvariance.h
#import <Cocoa/Cocoa.h>
#import <AspectCocoa/AspectCocoa.h>

@interface ACInvariance : NSObject
{} // No instance vars
+(void)loadAspectForClass:(NSString*)aClass;
-(void)before:(ACInvocation*)invocation;
-(void)after:(ACInvocation*)invocation;
@end

----
    
//ACInvariance.m
#import "ACInvariance.h"

@implementation ACInvariance
+(void)loadAspectForClass:(NSString*)aClass {
	
	id allClasses = [ACPointCut enumerateClassesNamed:aClass,nil];
	id eachClass, resultClasses;
	resultClasses = [[NSMutableArray alloc] init];
	while( eachClass = [allClasses nextObject] ) {
		if( class_getInstanceMethod([eachClass getClass],@selector(invariant)) != NULL){
			id allMethods = [eachClass methodEnumerator];
			id eachMethod;
			while(eachMethod = [allMethods nextObject]) {
				if( ! eachMethod methodName] hasPrefix:@"init"] && 
					! [[eachMethod methodName] hasPrefix:@"alloc"] &&
					! [[eachMethod methodName] hasPrefix:@"invariant"])
				{
					[eachClass addMethod: eachMethod];
				}
			}
			[resultClasses addObject:eachClass];
		}
	}
	id pointCut = [[[[ACPointCut alloc] initWithJoinPoints:resultClasses];
	ACAspect* invariantAspect = [[ACAspect alloc] 
                         initWithPointCut: pointCut  andAdviceObject: [[ACInvariance alloc]init]];
	[invariantAspect load];
	[invariantAspect autorelease];
}
-(void)before:(ACInvocation*)invocation 
{ 
	invocation target] invariant]; 
}
-(void)after:([[ACInvocation*)invocation 
{ 
	[[invocation target] invariant]; 
}
@end

