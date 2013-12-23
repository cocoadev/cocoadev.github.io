---
layout: page
title: ACMethodIterator
---

Just a class for convenience, see PoseAsAspects and LookupAspects for more info


**ACMethodIterator.h**

    
 #import <Foundation/Foundation.h>
 
 @interface ACMethodIterator : NSObject {
     Class theClass;
     void *iterator;
     struct objc_method_list *methodList;
     int i;
     int count;
     BOOL anotherMethod;
 }
 
 -(id) initWithClass: (Class) aclass;
 
 -(struct objc_method *)nextMethod;
 
 -(BOOL)thereIsAnotherMethod;
 
 @end



**ACMethodIterator.m**

    
 #import "ACMethodIterator.h"
 #import </usr/include/objc/objc-class.h>
 
 @implementation ACMethodIterator
 
 -(id) initWithClass: (Class) aclass{
     self = [super init];
     if (self) {
         theClass = aclass;
         iterator = 0;
         i = 0;
         methodList = class_nextMethodList(theClass, &iterator);
         count = methodList->method_count;
         if( methodList == NULL )
             anotherMethod = NO;
         else if( &methodList->method_list[0] == NULL )
             anotherMethod = NO;
         else
             anotherMethod = YES;
     }
     return self;
 }
 
 +(void)listMethods:(Class)aclass{
     ACMethodIterator * m = [[ACMethodIterator alloc] initWithClass: aclass];
     struct objc_method * method = [m nextMethod];
     while(method != NULL){
         NSLog(@"%s", method->method_name);
         method = [m nextMethod];
     }
 }
 
 -(BOOL)thereIsAnotherMethod{
     return anotherMethod;
 }
 
 -(struct objc_method *)nextMethod{
     if( !anotherMethod){
         return NULL;
     }
     struct objc_method * toReturn = &methodList->method_list[i];
     i++;
     if(i >= count){
         i = 0;
         methodList = class_nextMethodList(theClass, &iterator);
         if( methodList == NULL ){
             anotherMethod = NO;
         }else if( &methodList->method_list[0] == NULL ){
             anotherMethod = NO;
         }else{
             anotherMethod = YES;
             count = methodList->method_count;
         }
     }
     return toReturn;
 }
 
 @end

