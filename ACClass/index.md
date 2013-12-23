---
layout: page
title: ACClass
---




    
 /* 
     Initializes this ACClass object with some Class
 
     Note that an ACClass object when used to define pointcuts will apply only to that method
     In Objective-C class methods and instance methods are actually stored in two separate classes
     By default, a class and it's meta class have the same name.
 
     So, an ACClass object created from [NSObject class] won't include class methods, such as alloc
     such methods are accessible only via [NSObject class]->isa
     
     ACClass alloc] initWithClass: [NSObject class;
     ACClass alloc] initWithClass: [NSObject class]->isa];
 
 */
 -(id)initWithClass:(Class)aClass;
 
 /* 
     Returns whether or not this class is a meta class.
 */
 -(BOOL)isMetaClass;
 
 /* 
     Returns the Class wrapped by this ACClass
 */
 -(Class)getClass;
 
 /* 
     Returns an NSString representation of the class name
 */
 -(NSString *)getClassName;
 
 /* 
     Returns an NSEnumerator of ACMethod objects.
     For enumerating through all methods on the class (not it's meta class).
 */
 -(NSEnumerator *)methodEnumerator;
 
 
 /* 
     Returns an array of ACMethod objects for all methods in the wrapped Class
 */
 -(NSMutableArray *)allPossibleMethods;
 
 /* 
     Provides to the Aspect an array of ACMethod objects defining all methods that should be advised
 */
 -(NSMutableArray *)getMethods;
 
 /* 
     Add an ACMethod object to the list of methods provided to the aspect for advising.
 */
 -(void)addMethod:(ACMethod*)method;




[[Category:PointlessInformation

