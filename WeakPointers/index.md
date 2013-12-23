---
layout: page
title: WeakPointers
---



Basically a weak pointer is a non-retaining pointer but is safe from the standpoint that if the object being pointed to is dealloced then the pointer will be nulled out so that it does not point to an invalid object.

In order to accomplish this you need to override the dealloc on the object(s) in question in order to post a "aboutToDealloc" notifcation or such. This can't be done by patching (swizzling�see General/MethodSwizzling) the dealloc on the original class because this would affect ALL instances of that class. What we need to do is to dynamically create a subclass of the object's class, override the dealloc on that one and then swizzle the isa of the object to point to our new subclass.

As I understand it, this same technique is used by Apple's new General/NSKeyValueObserving pattern. Unfortunately, they do not support "dealloc" notification as I would like.

So far, I've been investigating and hacking various General/ObjCBridge frameworks (General/PyObjC, Ruby, ...) but these are cluttered with a good bit of their own requirements. So much so, that I have not (yet) been able to get any to perform as desired.

The basic API I want to support is...



* Class xobjc_createSubclass (Class super, const char *newClassName);
* BOOL xobjc_addNewMethod (Class classp, SEL methodSelector, IMP methodImplementation);
* BOOL xobjc_changeClass (id anObject, Class newClass);  // anObject->isa = newClass w/ some sanity checks


Any ideas/code would be most appreciated.

-jason

After some more searhing I found (source below) which works fine for his example (test_main) but not for mine. It still seems the superclass is still not properly wired in.

Thanks to http://www.ai.mit.edu/~gregs/info-dylan-archive-html-2001/msg00178.html

-jason


----
I updated the source below. It now works EXCEPT on General/CoreFoundation strings. I've already submitted the bug to apple. Any comments/improvements welcome.

*I think all General/CoreFoundation bridged classes are affected, e.g. also General/NSCFArray, General/NSCFDictionary etc.*

-jason

----

     

#import <Foundation/Foundation.h>
#include "stdio.h"

#import <objc/objc-class.h>

// TODO: What should we use instead of malloc, or is malloc OK?

id test_imp( id this, SEL selector, ... )
{
    printf( "hello\n" );
    
    return this;
}

id x_initialize ( id this, SEL selector, ... )
{
    printf( "I'm done\n" );
    return this;
}

Class General/MakeDynamicClass( const char * className, id superClass, int instanceSize, BOOL isMetaClass )
{
    Class myClass;
    struct objc_method_list ** emptyMethodLists;
    
    // We will add our methods later, so the list of lists starts with just end-of-list
    emptyMethodLists = malloc( sizeof(void *) );
    *emptyMethodLists = ((struct objc_method_list*)-1); // See objc-private.h in Darwin
    
    // Allocate and fill out the class information
    myClass = malloc( sizeof( struct objc_class ) );
    myClass->name = strdup (className);          
    myClass->version = 0;       // 0 OK
    myClass->instance_size = instanceSize;      //! sizeof(id) for isa 
    myClass->ivars = NULL;      // NULL OK
    myClass->methodLists = emptyMethodLists;
    myClass->cache = NULL;      // Allocated by runtime
    myClass->protocols = NULL;  // NULL OK
    
    // Make our isa. If we're a class, make a metaclass for our isa.
    if( isMetaClass )
    {
      Class root_class = superClass;
      while (root_class->isa && root_class != root_class->isa)
      	root_class = root_class->isa;

      //      Class bob = superClass->isa;
      //      Class hob = Nil;

      //      for (; bob && bob != hob; hob = bob, bob = bob->isa);
	//	printf ("%s<%0x> ", bob->name, (int)bob);

      myClass->isa = root_class;

      //        myClass->info = (CLS_META | CLS_NEED_BIND);
        myClass->info = (CLS_META);
        myClass->super_class = superClass->isa->super_class; // superClass->isa;
        // A meta class's isa pointer points the meta class of the root class
	//myClass->isa = superClass->isa;
        //myClass->isa = objc_getClass ("General/NSObject");
    }
    else
    {
        myClass->info = (CLS_CLASS);
	myClass->super_class = superClass;      
        myClass->isa = General/MakeDynamicClass( className, superClass, sizeof( id ), TRUE );
    }
    
    return myClass;
}

BOOL General/RegisterDynamicClass( const char * dynamicClassName, id superClass )
{
    BOOL result = FALSE;
    
    // Make sure we're not trying to overwrite an existing class
    if( objc_getClass( dynamicClassName ) == nil )
    {
       Class dynamicClass = General/MakeDynamicClass( dynamicClassName, superClass, sizeof( id ), FALSE );
        
       //       General/RegisterDynamicMethod( "initialize", dynamicClassName, x_initialize, "@4@4:8" );
        // Add the class to the runtime
        objc_addClass( dynamicClass );
    
        result = true;
    }
    
    return result;
}

// It is probably more efficient to register a single block of methods

BOOL General/RegisterDynamicMethod( const char * dynamicMethodName, const char * className, IMP method, char * methodTypes )
{
    BOOL result = FALSE;
    
    // Get the class object we want to add the method to
    id methodClass = objc_getClass( className );
    
    // Make sure the class we're trying to attach a method to exists
   if( methodClass != Nil )
    {
        struct objc_method_list * methodList = malloc( sizeof( struct objc_method_list ) );
        
        // Get or register the selector for the method name
        SEL methodSEL = SELUID( dynamicMethodName );
        // Registering the method seems to register the selector
        /*if( ISSELECTOR( methodSEL ) == FALSE )
        {
            methodSEL = sel_registerName( dynamicMethodName );
        }*/
    
        // Fill out the method list
        methodList->method_count = 1;
        methodList->method_list[ 0 ].method_name = methodSEL;
        methodList->method_list[ 0 ].method_types = methodTypes;
        methodList->method_list[ 0 ].method_imp = method;
        
        // Register our method
        class_addMethods(methodClass, methodList);
        
        result = true;
    }
        
    return result;
}

void test_main (int argc, char **argv)
{
    Class myClass;
    id theid;
    
    General/RegisterDynamicClass( "General/DynamicClass", objc_getClass( "General/NSObject" ) );
    myClass = objc_getClass( "General/DynamicClass" );
    // We cannot call to an undeclared class using []
    //General/[DynamicClass alloc];
    // We can call an undeclared class using objc_msgSend
    theid = [myClass alloc];
    [theid retain];
    
    General/RegisterDynamicMethod( "hello", "General/DynamicClass", test_imp, "@4@4:8" );
    General/RegisterDynamicMethod( "hello2", "General/DynamicClass", test_imp, "@4@4:8" );
    General/RegisterDynamicMethod( "hello3", "General/DynamicClass", test_imp, "@4@4:8" );
    General/RegisterDynamicMethod( "hello4", "General/DynamicClass", test_imp, "@4@4:8" );
    General/RegisterDynamicMethod( "hello5", "General/DynamicClass", test_imp, "@4@4:8" );

    [theid hello];
    [theid hello2];
    [theid hello3];
    [theid hello4];
    [theid hello5];
}

static
id my_dealloc ( id self, SEL _cmd, ... )
{
  Class cl = [self class];
  Method me = class_getInstanceMethod (cl->super_class, _cmd);

  fprintf (stderr, "%s: I'm melting....\n", General/self description] cString]);
  me->method_imp (self, _cmd);
}

@interface Foo : [[NSObject {
  int ndx;
}
@end
@implementation Foo
@end

@interface Bar : Foo
@end
@implementation Bar
@end

void patch_obj (id obj)
{
  char cname[128];

  sprintf (cname, "Dyn_%s", obj->isa->name);
  General/RegisterDynamicClass( cname, [obj class]);
  Class myClass = objc_getClass( cname );

  General/RegisterDynamicMethod( "dealloc", cname, my_dealloc, "@4@4:8" );

  ((Class)obj)->isa = myClass;
}

void
isa_chain (const char *name, id nob)
{
  Class bob = nob->isa;
  Class hob = Nil;

  printf ("%s:isa ", name);
  for (; bob && bob != hob; hob = bob, bob = bob->isa)
    printf ("%s<%0x> ", bob->name, (int)bob);
}
  

void
super_chain (const char *name, id nob)
{
  Class bob = nob->isa->super_class;
  Class hob = Nil;

  printf ("%s:super ", name);
  for (; bob && bob != hob; hob = bob, bob = bob->super_class)
    printf ("%s<%0x> ", bob->name, (int)bob);
}
  

int
main (int argc, const char **argv)
{
  General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];
  General/NSString *str_1, *str_2;
  Foo *foo;
  Bar *bar1, *bar2;

  foo = General/Foo alloc] init];
  bar1 = [[Bar alloc] init];
  bar2 = [[Bar alloc] init];

  str_1 = [[[[NSString alloc] initWithCString:"string 1"];
  str_2 = General/[[NSString alloc] initWithCString:"string 2"];

  patch_obj (str_2);

  isa_chain ("str_1", str_1); printf ("\n");
  isa_chain ("str_2", str_2); printf ("\n");
  printf ("\n");


  super_chain ("str_1", str_1); printf ("\n");
  super_chain ("str_2", str_2); printf ("\n");
  printf ("\n");

  isa_chain ("bar1", bar1); printf ("\n");
  patch_obj (bar1);
  isa_chain ("bar1", bar1); printf ("\n");
  isa_chain ("bar2", bar2); printf ("\n");

  printf ("\n");

  super_chain ("bar1", bar1); printf ("\n");
  super_chain ("bar2", bar2); printf ("\n");


  patch_obj (foo);
  patch_obj (bar2);
  [foo release];
  [bar1 release];
  [bar2 release];

  [str_1 release];
  
  // [str_2 release];  // BAD THINGS HAPPEN HERE!!!

  [pool release];
  return 0;
}

 

----

This is a very interesting post, but I have a few questions. From my experience with the General/ObjectiveC General/RunTime, method types take the form [return type][return type offset][id][id offset][SEL][SEL offset][firstvar][firstvar offset]..., but the return type and offset are physically located in the stack frame after the other arguments in the method (including id and SEL). Maybe this is just an Apple convention, and technically it shouldn't matter, but it might be a good idea to conform to that standard anyway. At any rate, whether the aforementioned is pertinant or not, I don't think the method types are being defined correctly. -(void)initialize should have the method type v8@0:4 and -(void)dealloc should be the same. This is the method type for any method returning void and accepting two arguments (an id and a SEL). Currently, the method type that is being used is @4@4:8 which would mean that the first four bytes of the method's stack frame would be unused. Additionally, the return value and first argument (I believe in this case) would share the same memory location... I have no idea what impact this could have on the runtime environment now or in the future.

- General/EliotSimcoe
