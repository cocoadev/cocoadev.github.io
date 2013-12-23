---
layout: page
title: ClassDeconstruction
---



**Note: The title of this page is a bit misleading. The actual topic has to do with a small part of introspection. Not class deallocation/destruction and dealloc method semantics.**

The ObjC runtime stores a lot of information about a class - namely, all its methods, instance variables, and super-classes. This information is freely available to programmers, and is often more comprehensive than Apple's documentation about its classes, for instance. Did you know that NSArray has a method called indexOf:::? I didn't, nor do I know what it does...

Anyway, here's the code that does it all:

    
#import </usr/include/objc/objc-class.h>
#import </usr/include/objc/Protocol.h>
#import <Foundation/Foundation.h>

void DeconstructClass(Class aClass)
{
    struct objc_class *class = aClass;
    const char *name = class->name;
    int k;
    void *iterator = 0;
    struct objc_method_list *mlist;

    NSLog(@"Deconstructing class %s, version %d",
          name, class->version);
    NSLog(@"%s size: %d", name,
          class->instance_size);
    if (class->ivars == nil)
        NSLog(@"%s has no instance variables", name);
    else
        {
        NSLog(@"%s has %d ivar%c", name,
              class->ivars->ivar_count,
              ((class->ivars->ivar_count == 1)?' ':'s'));
        for (k = 0;
             k < class->ivars->ivar_count;
             k++)
            NSLog(@"%s ivar #%d: %s", name, k,
                  class->ivars->ivar_list[k].ivar_name);
        }
    mlist = class_nextMethodList(aClass, &iterator);
    if (mlist == nil)
        NSLog(@"%s has no methods", name);
    else do
        {
            for (k = 0; k < mlist->method_count; k++)
                {
                NSLog(@"%s implements %@", name,
                      NSStringFromSelector(mlist->method_list[k].method_name));
                }
        }
        while ( mlist = class_nextMethodList(aClass, &iterator) );

    if (class->super_class == nil)
        NSLog(@"%s has no superclass", name);
    else
        {
        NSLog(@"%s superclass: %s", name, class->super_class->name);
        DeconstructClass(class->super_class);
        }
}


----

Here is a sample of its talents (on Mac OS X 10.1.3):

    DeconstructClass([NSObject class]);
produces:
    
Deconstructing class NSObject, version 0
NSObject size: 4
NSObject has 1 ivar 
NSObject ivar #0: isa
NSObject implements _conformsToProtocolNamed:
NSObject implements replacementObjectForPortCoder:
NSObject implements classForPortCoder
NSObject implements _cfTypeID
NSObject implements methodFor:
NSObject implements conformsTo:
NSObject implements respondsTo:
NSObject implements isKindOf:
NSObject implements forward::
NSObject implements isNSIDispatchProxy
NSObject implements replacementObjectForArchiver:
NSObject implements classForArchiver
NSObject implements performSelector:withObject:afterDelay:inModes:
NSObject implements performSelector:object:afterDelay:
NSObject implements performSelector:withObject:afterDelay:
NSObject implements coerceValue:forKey:
NSObject implements removeValueAtIndex:fromPropertyWithKey:
NSObject implements insertValue:atIndex:inPropertyWithKey:
NSObject implements replaceValueAtIndex:inPropertyWithKey:withValue:
NSObject implements valueWithUniqueID:inPropertyWithKey:
NSObject implements valueWithName:inPropertyWithKey:
NSObject implements valueAtIndex:inPropertyWithKey:
NSObject implements classCode
NSObject implements className
NSObject implements objectSpecifier
NSObject implements isCaseInsensitiveLike:
NSObject implements isLike:
NSObject implements doesContain:
NSObject implements isGreaterThan:
NSObject implements isGreaterThanOrEqualTo:
NSObject implements isLessThan:
NSObject implements isLessThanOrEqualTo:
NSObject implements isNotEqualTo:
NSObject implements isEqualTo:
NSObject implements removeObject:fromBothSidesOfRelationshipWithKey:
NSObject implements addObject:toBothSidesOfRelationshipWithKey:
NSObject implements _setObject:forBothSidesOfRelationshipWithKey:
NSObject implements removeObject:fromPropertyWithKey:
NSObject implements addObject:toPropertyWithKey:
NSObject implements clearProperties
NSObject implements allPropertyKeys
NSObject implements isToManyKey:
NSObject implements validateTakeValue:forKeyPath:
NSObject implements validateValue:forKey:
NSObject implements classDescriptionForDestinationKey:
NSObject implements ownsDestinationObjectsForRelationshipKey:
NSObject implements inverseForRelationshipKey:
NSObject implements toManyRelationshipKeys
NSObject implements toOneRelationshipKeys
NSObject implements attributeKeys
NSObject implements entityName
NSObject implements classDescription
NSObject implements createKeyValueBindingForKey:typeMask:
NSObject implements _createKeyValueBindingForKey:name:bindingType:
NSObject implements flushKeyBindings
NSObject implements keyValueBindingForKey:typeMask:
NSObject implements takeStoredValue:forKey:
NSObject implements takeValue:forKey:
NSObject implements storedValueForKey:
NSObject implements valueForKey:
NSObject implements unableToSetNilForKey:
NSObject implements handleTakeValue:forUnboundKey:
NSObject implements handleQueryWithUnboundKey:
NSObject implements takeStoredValuesFromDictionary:
NSObject implements takeValuesFromDictionary:
NSObject implements valuesForKeys:
NSObject implements takeValue:forKeyPath:
NSObject implements valueForKeyPath:
NSObject implements perform:withEachObjectInArray:
NSObject implements implementsSelector:
NSObject implements methodForSelector:
NSObject implements doesNotRecognizeSelector:
NSObject implements replacementObjectForCoder:
NSObject implements classForCoder
NSObject implements awakeAfterUsingCoder:
NSObject implements performv::
NSObject implements forwardInvocation:
NSObject implements methodSignatureForSelector:
NSObject implements forward::
NSObject implements methodDescriptionForSelector:
NSObject implements mutableCopy
NSObject implements init
NSObject implements dealloc
NSObject implements copy
NSObject implements zone
NSObject implements superclass
NSObject implements self
NSObject implements retainCount
NSObject implements retain
NSObject implements respondsToSelector:
NSObject implements release
NSObject implements performSelector:withObject:withObject:
NSObject implements performSelector:withObject:
NSObject implements performSelector:
NSObject implements isProxy
NSObject implements isFault
NSObject implements isMemberOfClass:
NSObject implements isKindOfClass:
NSObject implements isEqual:
NSObject implements hash
NSObject implements _copyDescription
NSObject implements debugDescription
NSObject implements description
NSObject implements conformsToProtocol:
NSObject implements class
NSObject implements autorelease
NSObject has no superclass


----

Anybody have any luck making this work in 10.3? It just SIGBUSs on me at class_nextMethodList.

OSX 10.3.4 with XCode 1.5 works like a charm.

----

Thats as very cool function! With a couple of trivial modifications it becomes a Category that you makes all objects able to deconstruct themselves:

**
ClassDeconstruction.h
**
    
#import <Cocoa/Cocoa.h>

@interface NSObject (ClassDeconstruction)

- (void)deconstruct;
- (void)deconstruct: (Class)aClass;

@end


**
ClassDeconstruction.m
**
    
#import "ClassDeconstruction.h"
#import </usr/include/objc/objc-class.h>
#import </usr/include/objc/Protocol.h>
#import <Foundation/Foundation.h>

@implementation NSObject (ClassDeconstruction)

- (void)deconstruct;
{
    [self deconstruct: [self class]];
}

- (void)deconstruct: (Class)aClass;
{
    struct objc_class *class = aClass;
    const char *name = class->name;
    int k;
    void *iterator = 0;
    struct objc_method_list *mlist;
    
    NSLog(@"Deconstructing class %s, version %d",
          name, class->version);
    NSLog(@"%s size: %d", name,
          class->instance_size);
    if (class->ivars == nil)
        NSLog(@"%s has no instance variables", name);
    else
    {
        NSLog(@"%s has %d ivar%c", name,
              class->ivars->ivar_count,
              ((class->ivars->ivar_count == 1)?' ':'s'));
        for (k = 0;
             k < class->ivars->ivar_count;
             k++)
            NSLog(@"%s ivar #%d: %s", name, k,
                  class->ivars->ivar_list[k].ivar_name);
    }
    mlist = class_nextMethodList(aClass, &iterator);
    if (mlist == nil)
        NSLog(@"%s has no methods", name);
    else do
    {
        for (k = 0; k < mlist->method_count; k++)
        {
            NSLog(@"%s implements %@", name,
                  NSStringFromSelector(mlist->method_list[k].method_name));
        }
    }
        while ( mlist = class_nextMethodList(aClass, &iterator) );
    
    if (class->super_class == nil)
        NSLog(@"%s has no superclass", name);
    else
    {
        NSLog(@"%s superclass: %s", name, class->super_class->name);
        [self deconstruct: class->super_class];
    }
}

@end


Then use the category method like this: <code>[self deconstruct];</code>


----

I used the code below at some point. Not as clean as above, but can be useful too (there is more done with ivars, and some info about the method signatures). Sorry I am too lazy to make it a category right now (and I don't want to break what works...). --CharlesParnot

    

**TFUtilities.h**

#import <Foundation/Foundation.h>
@interface TFUtilities : NSObject {}
+ (NSArray *)methodsOfObject:(id)anObject classObject:(BOOL)flag;
+ (NSArray *)ivarsOfObject:(id)anObject classObject:(BOOL)flag;
+ (NSString *)describeObject:(id)anObject classObject:(BOOL)flag;
@end


**TFUtilities.m**
#import "TFUtilities.h"
#import <objc/objc.h>
#import <objc/objc-class.h>
#import <objc/objc-runtime.h>

static NSString* TFMethodNameKey=@"method_name";
static NSString* TFMethodTypesKey=@"method_types";
static NSString* TFIvarNameKey=@"ivar_name";
static NSString* TFIvarTypeKey=@"ivar_type";
static NSString* TFIvarOffsetKey=@"ivar_offset";
static NSString* TFIvarObjectKey=@"ivar_object";

@implementation TFUtilities


//returns an NSArray of NSDictionary
//each item in the array = 1 ivar
//dictionary = description of the ivar
+ (NSArray *)ivarsOfObject:(id)anObject classObject:(BOOL)flag
{	
	NSMutableArray *ivars;
	NSDictionary *info;
	NSString *name,*type;
	int i,n;
	struct objc_class *class;
	struct objc_ivar_list* ivar_list_struct;
	struct objc_ivar *ivar_list;
	struct objc_ivar ivar;
	id ivarObject;
	
	if (anObject==nil)
		return [NSArray array];
	
	//get the objc_ivar_list
	if (flag)
		class=anObject;
	else
		class=[anObject class];
	ivar_list_struct=(*class).ivars;
	if (ivar_list_struct==NULL)
		return [NSArray array];
	n=ivar_list_struct->ivar_count;
	ivar_list=ivar_list_struct->ivar_list;
	ivars=[NSMutableArray arrayWithCapacity:n];
	
	//get the ivars info
	for (i=0;i<n;i++) {
		ivar=ivar_list[i];
		name=[NSString stringWithCString:ivar.ivar_name];
		type=[NSString stringWithCString:ivar.ivar_type];

		if(!flag && type substringToIndex:1] isEqualToString:@"@"])
			object_getInstanceVariable(anObject,ivar.ivar_name,(void **)&ivarObject);
		else
			ivarObject=[[[NSNull null];
		info=[NSDictionary dictionaryWithObjectsAndKeys:
			name,TFIvarNameKey,
			type,TFIvarTypeKey,
			[NSNumber numberWithInt:ivar.ivar_offset],TFIvarOffsetKey,
			ivarObject,TFIvarObjectKey,
			nil];
		[ivars addObject:info];
	}
	
	return ivars copy] autorelease];
}

//returns an [[NSArray of NSDictionary
//each item in the array = 1 method
//dictionary = description of the method
+ (NSArray *)methodsOfObject:(id)anObject classObject:(BOOL)flag
{	
	NSMutableArray *methods;
	NSDictionary *info;
	int i,n;
	struct objc_class *class;
	struct objc_method *method;
	void *iterator = 0;
	struct objc_method_list *methodList;
	
	if (anObject==nil)
		return [NSArray array];
	if (flag)
		class=anObject;
	else
		class=[anObject class];
	
	//count methods
	n=0;
	while( methodList = class_nextMethodList(class, &iterator ) )
		n+=(*methodList).method_count;
	methods=[NSMutableArray arrayWithCapacity:n];
	
	//retrieve method info
	while( methodList = class_nextMethodList([anObject class], &iterator ) ) {
		n=(*methodList).method_count;
		for (i=0;i<n;i++) {
			method=(*methodList).method_list + i;
			info=[NSDictionary dictionaryWithObjectsAndKeys:
				NSStringFromSelector(method->method_name),TFMethodNameKey,
				[NSString stringWithCString:method->method_types],TFMethodTypesKey,
				nil];
			[methods addObject:info];
		}
	}
	return methods copy] autorelease];
}

+ ([[NSString *)describeObject:(id)anObject classObject:(BOOL)flag
{
	NSEnumerator *e;
	NSDictionary *dico;
	id class,sup;
	NSArray *classMethods,*methods,*ivars;
	NSMutableString *description=[NSMutableString string];
	
	
	//initializations
	if (flag)
		class=anObject;
	else
		class=[anObject class];
	id metaclass=objc_getMetaClass([NSStringFromClass(class) UTF8String]);
	classMethods=[self methodsOfObject:metaclass classObject:YES];
	methods=[self methodsOfObject:anObject classObject:flag];
	ivars=[self ivarsOfObject:anObject classObject:flag];
	
	//class and superclasses
	[description appendString:[NSString stringWithFormat:@"\n%@object <%@:%p>\n",
		(flag?@"class ":@""),class,anObject]];
	[description appendString:@"\n"];
	[description appendString:[NSString stringWithFormat:@"description:\n%@\n",anObject]];
	[description appendString:@"\n"];
	[description appendString:[NSString stringWithFormat:@"class: %@\n",class]];
	sup=[class superclass];
	[description appendString:[NSString stringWithFormat:@"super1: %@\n",sup]];
	sup=[sup superclass];
	[description appendString:[NSString stringWithFormat:@"super2: %@\n",sup]];
	sup=[sup superclass];
	[description appendString:[NSString stringWithFormat:@"super3: %@\n",sup]];
	sup=[sup superclass];
	[description appendString:[NSString stringWithFormat:@"super4: %@\n",sup]];
	sup=[sup superclass];
	[description appendString:[NSString stringWithFormat:@"super5: %@\n",sup]];
	
	//methods
	[description appendString:@"\n"];
	[description appendString:@"class methods:\n"];
	e=[classMethods objectEnumerator];
	while (dico=[e nextObject])
		[description appendFormat:@"   %@ (%@)\n",
			[dico objectForKey:TFMethodNameKey],[dico objectForKey:TFMethodTypesKey]];
	[description appendString:@"\n"];
	[description appendString:@"instance methods:\n"];
	e=[methods objectEnumerator];
	while (dico=[e nextObject])
		[description appendFormat:@"   %@ (%@)\n",
			[dico objectForKey:TFMethodNameKey],[dico objectForKey:TFMethodTypesKey]];
	
	//ivars
	[description appendString:@"\n"];
	[description appendString:@"ivars:\n"];
	e=[ivars objectEnumerator];
	while (dico=[e nextObject]) {
		[description appendFormat:@"   %@   = %@ at offset %@\n",
			[dico objectForKey:TFIvarNameKey],
			[dico objectForKey:TFIvarTypeKey],
			[dico objectForKey:TFIvarOffsetKey]];
		/*
		id ivarObject=[dico objectForKey:TFIvarObjectKey];
		if (ivarObject!=[NSNull null])
			[description appendFormat:@"      object=<%@:%p>\n",[ivarObject class],ivarObject];
		 */
	}
	
	[description appendString:@"\n\n"];
	return [NSString stringWithString:description];
}
@end


