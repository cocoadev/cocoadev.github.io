---
layout: page
title: CodeRefactorChallenge
---

CodeRefactorChallenge - the solving of this problem is specicifically related to improving the size of AspectCocoa.

I challenge any developer to succesfully refactor the following code into shorter methods (and thus, a presumably smaller compiled size)
It all looks very repetetive, so there should be some way to condense it...  right?

it seems the solution may be to completely rework AspectCocoa, like this: ForwardInvocationAspect
    

typedef union {
    NSSize size;
    NSRect rect;
    NSPoint point;
    NSRange range;
}UnionOfNSTypes;

typedef double ACDoubleType;
typedef char ACCharType;
typedef void * ACPointerType;
typedef UnionOfNSTypes ACStructType;
typedef char * ACCharStarType;
//typedef BOOL ACBOOLType; //same as char

typedef ACDoubleType (*ACDoubleIMP)(id, SEL, ...);
typedef ACCharType (*ACCharIMP)(id, SEL, ...);
typedef ACPointerType (*ACPointerIMP)(id, SEL, ...);
typedef ACStructType (*ACStructIMP)(id, SEL, ...);
typedef ACCharStarType (*ACCharStarIMP)(id, SEL, ...);
//typedef ACBOOLType (*ACBOOLIMP)(id, SEL, ...); //same as char



and..

    


-(ACCharStarType)replacCharStarPoint: (ACPointerType)a Point: (ACPointerType)b Char: (ACCharType)c {
    ACCharStarType toReturn;
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil){ NSLog(@"!! error this should never happen! advice list was nil"); }
    [advice invokeBefores:_cmd onObject: self arg1: &a arg2: &b arg3: &c arg4: nil];
    ACCharStarIMP toInvoke = (ACCharStarIMP)[advice getIMP];
    if([advice hasArounds])
        toReturn = toInvoke(self, _cmd, a, b, c, [advice aroundIMPs]);
    else
        toReturn = toInvoke(self, _cmd, a, b, c);
    [advice invokeAfters:_cmd onObject: self returned: &toReturn arg1: &a arg2: &b arg3: &c arg4: nil];
    return toReturn;
}


-(ACCharStarType)replacCharStarPoint: (ACPointerType)a Point: (ACPointerType)b CharStar: (ACCharStarType)c {
    ACCharStarType toReturn;
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil){ NSLog(@"!! error this should never happen! advice list was nil"); }
    [advice invokeBefores:_cmd onObject: self arg1: &a arg2: &b arg3: &c arg4: nil];
    ACCharStarIMP toInvoke = (ACCharStarIMP)[advice getIMP];
    if([advice hasArounds])
        toReturn = toInvoke(self, _cmd, a, b, c, [advice aroundIMPs]);
    else
        toReturn = toInvoke(self, _cmd, a, b, c);
    [advice invokeAfters:_cmd onObject: self returned: &toReturn arg1: &a arg2: &b arg3: &c arg4: nil];
    return toReturn;
}


-(ACCharStarType)replacCharStarPoint: (ACPointerType)a Point: (ACPointerType)b Struct: (ACStructType)c {
    ACCharStarType toReturn;
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil){ NSLog(@"!! error this should never happen! advice list was nil"); }
    [advice invokeBefores:_cmd onObject: self arg1: &a arg2: &b arg3: &c arg4: nil];
    ACCharStarIMP toInvoke = (ACCharStarIMP)[advice getIMP];
    if([advice hasArounds])
        toReturn = toInvoke(self, _cmd, a, b, c, [advice aroundIMPs]);
    else
        toReturn = toInvoke(self, _cmd, a, b, c);
    [advice invokeAfters:_cmd onObject: self returned: &toReturn arg1: &a arg2: &b arg3: &c arg4: nil];
    return toReturn;
}


-(ACCharStarType)replacCharStarPoint: (ACPointerType)a Point: (ACPointerType)b Point: (ACPointerType)c {
    ACCharStarType toReturn;
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil){ NSLog(@"!! error this should never happen! advice list was nil"); }
    [advice invokeBefores:_cmd onObject: self arg1: &a arg2: &b arg3: &c arg4: nil];
    ACCharStarIMP toInvoke = (ACCharStarIMP)[advice getIMP];
    if([advice hasArounds])
        toReturn = toInvoke(self, _cmd, a, b, c, [advice aroundIMPs]);
    else
        toReturn = toInvoke(self, _cmd, a, b, c);
    [advice invokeAfters:_cmd onObject: self returned: &toReturn arg1: &a arg2: &b arg3: &c arg4: nil];
    return toReturn;
}


-(ACStructType)replacStructDouble: (ACDoubleType)a Double: (ACDoubleType)b Double: (ACDoubleType)c {
    ACStructType toReturn;
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil){ NSLog(@"!! error this should never happen! advice list was nil"); }
    [advice invokeBefores:_cmd onObject: self arg1: &a arg2: &b arg3: &c arg4: nil];
    ACStructIMP toInvoke = (ACStructIMP)[advice getIMP];
    if([advice hasArounds])
        toReturn = toInvoke(self, _cmd, a, b, c, [advice aroundIMPs]);
    else
        toReturn = toInvoke(self, _cmd, a, b, c);
    [advice invokeAfters:_cmd onObject: self returned: &toReturn arg1: &a arg2: &b arg3: &c arg4: nil];
    return toReturn;
}


-(ACStructType)replacStructDouble: (ACDoubleType)a Double: (ACDoubleType)b Char: (ACCharType)c {
    ACStructType toReturn;
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil){ NSLog(@"!! error this should never happen! advice list was nil"); }
    [advice invokeBefores:_cmd onObject: self arg1: &a arg2: &b arg3: &c arg4: nil];
    ACStructIMP toInvoke = (ACStructIMP)[advice getIMP];
    if([advice hasArounds])
        toReturn = toInvoke(self, _cmd, a, b, c, [advice aroundIMPs]);
    else
        toReturn = toInvoke(self, _cmd, a, b, c);
    [advice invokeAfters:_cmd onObject: self returned: &toReturn arg1: &a arg2: &b arg3: &c arg4: nil];
    return toReturn;
}


-(ACStructType)replacStructDouble: (ACDoubleType)a Double: (ACDoubleType)b CharStar: (ACCharStarType)c {
    ACStructType toReturn;
    ACAdviceList * advice = [[ACAspectManager sharedManager] adviceListForSelector: _cmd onObject: self];
    if(advice == nil){ NSLog(@"!! error this should never happen! advice list was nil"); }
    [advice invokeBefores:_cmd onObject: self arg1: &a arg2: &b arg3: &c arg4: nil];
    ACStructIMP toInvoke = (ACStructIMP)[advice getIMP];
    if([advice hasArounds])
        toReturn = toInvoke(self, _cmd, a, b, c, [advice aroundIMPs]);
    else
        toReturn = toInvoke(self, _cmd, a, b, c);
    [advice invokeAfters:_cmd onObject: self returned: &toReturn arg1: &a arg2: &b arg3: &c arg4: nil];
    return toReturn;
}



etc...
there needs to be code like this for all possible combinations of return types and arg types from 0 to 4 args...
so how can it be condensed? any ideas?  Right now all possible combinations compiled come out to more than 5 MB.  For a framework that I expect developers to add to their own apps, this is unreasonable.  And I would like to support more args.. 5, 6, 7 each addition of 1 arg will greatly increase the size.. (by at least a factor of 2)  ACProblemOfVariableTypes provides code that will generate the above code in full..

----

Have a look at forwardInvocation: and methodSignatureForSelector: � this should allow you to create a catch-all method which will sort out what actually needs to be done, based on the method signature.

