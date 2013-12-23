---
layout: page
title: ACMethod
---



    

/*
    Initializes this ACMethod object with a particular Method and Class
*/
-(id) initWithMethod:(struct objc_method *)aMethod andClass:(Class)c;

/*
    Returns the wrapped method
*/
-(struct objc_method *) getMethod;

/*
    Returns the signature of the wrapped method
*/
-(NSMethodSignature *)getSignature;

/*
    Returns an NSString representation of the wrapped method's name
*/
-(NSString *)methodName;


