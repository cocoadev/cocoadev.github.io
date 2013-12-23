---
layout: page
title: PointCut
---

A PointCut is an object that defines a set of methods and classes, by responding to the selector     joinPoints

    joinPoints is expected to return an NSArray of ACClass objects, each containing some number of ACMethod objects.

The helper class ACPointCut may be used as follows to help create a point cut.  This particular example picks from all classes beginning with 'Test' all methods ending in ':'
    
    NSMutableArray * joinPoints = [NSMutableArray new];
    NSEnumerator *classEnumerator = [ACPointCut enumerateClassesNamed: @"TestClass", @"TestSuper", nil];
    ACClass * acclass;
    while (acclass = [classEnumerator nextObject]) {
	if(acclass className] hasPrefix: @"Test"]){ //Perform some test on acclass
	    [joinPoints addObject: acclass];
	    [[NSEnumerator * methodEnumerator = [acclass methodEnumerator];
	    ACMethod * acmethod;
	    while (acmethod = [methodEnumerator nextObject]) {
		if(acmethod methodName] hasSuffix: @":"]){ //Perform some test on acmethod
		    [acclass addMethod: acmethod];
		}
	    }
	}
    }

    [[ACPointCut * pointCut = [ACPointCut pointCutWithJoinPoints: joinPoints];


or, it may be preferable to implement a     joinPoints method on some relevant class.
note that in this case we are using the method signature to check that it has more than one argument, rather than checking that it ends in a ':'
    
- (id) joinPoints{
    NSMutableArray * joinPoints = [NSMutableArray new];
    NSEnumerator *classEnumerator = [ACPointCut enumerateClassesNamed: @"TestClass", @"TestSuper", nil];
    ACClass * acclass;
    while (acclass = [classEnumerator nextObject]) {
	if(acclass className] hasPrefix: @"Test"]){ //Perform some test on acclass
	    [joinPoints addObject: acclass];
	    [[NSEnumerator * methodEnumerator = [acclass methodEnumerator];
	    ACMethod * acmethod;
	    while (acmethod = [methodEnumerator nextObject]) { 
		if(acmethod getSignature] numberOfArguments] > 2){ //Perform some test on acmethod
		    [acclass addMethod: acmethod];
		}
	    }
	}
    }
    return joinPoints;
}


As an alternative to using      [[[ACPointCut enumerateClassesNamed: ... ] you can use     enumerateAllClasses to go through every class in the runtime, or     enumerateDefaultClasses to go through every class in the runtime which is not a part of foundation or appkit

