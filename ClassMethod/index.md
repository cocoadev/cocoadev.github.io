---
layout: page
title: ClassMethod
---



A ClassMethod is a method performed by a ClassObject.

Typically, a ClassMethod is used to create a new instance (new InstanceObject) of the receiving class, and to perform management upon any InstanceObject of that class.

A ClassMethod can always be invoked.  You do not create an instance of the class to which to send the ClassMethod.

You invoke a ClassMethod by sending a message to the class, like this:

    
[SomeClass someMethod];


----

You can have a ClassMethod and an InstanceMethod with the same signature (except, of course, for the + and -):

    
+ (NSString *)name {
     return @"Test";
}

- (NSString *)name {
     return self class] name];
}


----

You could also do this:

    

#define [[BothMethods(Name,ReturnType,SingeLine) -(ReturnType)Name { singleLine; } +(ReturnType)Name{ singleLine; }

...

BothMethods(Name,NSString,return @"Test")



or

    

#define MatchToClass(ReturnType,MethodName) -(ReturnType)MethodName { return self class] [[MethodName]; }

+ (int)stuff { return 5+5; }
MatchToClass(int,stuff);



-FranciscoTolmasky

