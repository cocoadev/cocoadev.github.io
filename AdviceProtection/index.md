---
layout: page
title: AdviceProtection
---

AdviceProtection - part of AspectCocoa and AspectCocoaDocumentation

Often times when writting an aspect, we may run into infinite loops of execution.  Here's simple example:

Suppose we load an aspect on all methods of one class whose purpose is to call invariant before and after every method of that class executes. (see InvarianceCheckingWithAspectCocoa).  So, we might create the point cut for such as aspect as such:

    
        id allClasses = [ACPointCut enumerateClassesNamed: @"MyTestClass", nil];
        id eachClass, resultClasses;
        resultClasses = [[NSMutableArray alloc] init];
        while( eachClass = [allClasses nextObject] ) {
                        id allMethods = [eachClass methodEnumerator];
                        id eachMethod;
                        while(eachMethod = [allMethods nextObject]) {
                            if( ! eachMethod methodName] hasPrefix:@"init"] && 
                                ! [[eachMethod methodName] hasPrefix:@"alloc"])
                                {
                                        [eachClass addMethod: eachMethod];
                                }
                        }
                        [resultClasses addObject:eachClass];
                }
        }
        id pointCut = [[[[ACPointCut alloc] initWithJoinPoints:resultClasses];


Now, the problem with this is, that this point cut will also include the method **invariant** on MyTestClass, so when a call is made to invariant, it will be intercepted and a call will be made to advice, which will in turn call invariant, which will be intercepted, adviced, etc... infinite loop.  So we can fix this by changing:
    
                            if( ! eachMethod methodName] hasPrefix:@"init"] && 
                                ! [[eachMethod methodName] hasPrefix:@"alloc"])

to also exclude the method **invariant**
    
                            if( ! [[eachMethod methodName] hasPrefix:@"init"] && 
                                ! [[eachMethod methodName] hasPrefix:@"alloc"] &&
                                ! [[eachMethod methodName] isEqualToString:@"invariant"])


Still, we may have a slight problem.
Suppose invariant was implemented like this:
    
-(void)invariant { 
    [[NSLog(@"test test I am now supposed to check invariance on %@", self);
}

looks alright at first glance, but what if **description** is also a method implemented by MyTestClass. In this case, AspectCocoa will also intercept **description** and call invariant before and after (Another infinite loop)  We might try excluding **description** from the pointCut, but we'd also have to exclude every method description calls, and every method those methods call and so forth.  And if we did so, we would no longer be able to check the invariant on some methods we really do want to check.

The solution is protected advice, and it has 2 forms.

By default (As of the latest version of AspectCocoa posted 5/18/2004), AspectCocoa will block all other advice from executing while one piece of advice is executing.
This behavior is controlled by methods on ACAspectManager.
**protectAdvice** may be called to enable this behavior and **unprotectAdvice** may be called to disable it.

If such global protection is undesirable, you can protect advice locally by calling **disableAllAdvice** before, and **enableAllAdvice** after you describe your advice, for instance:

    
-(void)before:(ACInvocation*)invocation {
    [ACAspectManager disableAllAdvice];
    invocation target] invariant];
    [[[ACAspectManager enableAllAdvice];
}

-(void)after:(ACInvocation*)invocation {
    [ACAspectManager disableAllAdvice];
    invocation target] invariant];
    [[[ACAspectManager enableAllAdvice];
}


Using protectAdvice and unprotectAdvice provide a way to have the above behavior in place for all of your advice. When unprotectAdvice is active it is possible to protect specific advice calls as shown, but when protectAdvice is active calling enableAllAdvice will have no effect.

