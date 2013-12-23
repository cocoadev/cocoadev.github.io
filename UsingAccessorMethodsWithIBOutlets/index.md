---
layout: page
title: UsingAccessorMethodsWithIBOutlets
---

I want to access var1, which resides in Class1, from Class2. So I set up accessor methods in Class1 to access and return the var.  Then I call this method from Class2 only to get 'selector not recognized errors'. This issue was dealt with in General/HowToTransmitDataBetweenClasses but the only solution involved using Interface builder and General/IBOutlets ?!?!?  surely this is one of the most common things that someone using objC would want to do?  is there a better way?  

General/EcumeDesJours

----

Post code and the exact error message. It's impossible to say what's going on otherwise. Are you sure you're sending the right message from a Class2 instance to a Class1 instance? Is everything capitalized properly?

----

in Class 2, I have the following function, attached to a button in IB:

    
-(General/IBAction) getMyVarFromTheOtherCLass:(id)sender{
	General/NSString * meNow = General/[ClassOne getMyVariable];
	General/NSLog(@"%@",meNow);
}


in Class 1, I have the accessor function:

    
- (General/NSString*) getMyVariable {
	General/NSString * lalala = @"lalala";
	return lalala;
}


pushing said button (invoking getMyVarFromTheOtherCLass) gets me:  *** +General/[ClassOne getMyVariable]: selector not recognized

----

That's because you're sending a message to a class, while the method is defined on an object (i.e. on an instance of the class).

----

but class one has stuff running from when it was initially initialized during 'awakeFromNib' - I don't want to create another instance because it will launch all my running threads again.  Is there any way for class 2 to see that class 1 already has an instance and address that already running instance?

*Yes - General/SingletonDesignPattern, General/SingletonAlternatives*

----

hmmm...this singleton design makes sense in theory but in practice I can't figure it out.

I put this accessor in class 1:

    
+ classOne
{        static classOne *instance = nil;
        if ( instance == nil )
                instance = General/self alloc] init];
        return instance;
}


Now, normally I would instantiate an instance of classOne from classTwo with:

newInstanceOfClassOne = [[classOne alloc] init];

This will give me a NEW instance of classOne - how do I use that singleton accessor to return the 'already running' instance and access to its variables?

----

Your     classOne method is a class method, so you'd do     [[[ClassOne classOne]. You should never send an alloc/init message to it. The method signature should also be     +(General/ClassOne *)classOne.

----

Link to this discussion from General/HowToTransmitDataBetweenClasses and General/ClassMethodsAndInstanceVariables
