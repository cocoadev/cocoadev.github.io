---
layout: page
title: ACPointCut
---

    
 
 /* 
     Returns a point cut object for all methods on all classes in the runtime, 
     except for those prefixed with AC and core foundation bridged classes.
 */
 -(id)init;
 
 /* 
     Returns a point cut object for all methods on all classes in the runtime
     except for anything in Foundation, Appkit, or Aspect Cocoa. (as defined by a prefix of NS %NS _NS etc...)
     This typically implies all user defined classes.
 */
 -(id)initDefault;
 
 /* 
     Returns a point cut object for all classes in the runtime, 
     except for those prefixed with AC and core foundation bridged classes.
 */
 -(id)initWithJoinPoints:(NSArray *)joinPoints;
 
 /* 
     Returns an NSEnumerator of ACClass objects.
     For enumerating through all classes, the same as init
 */
 +(NSEnumerator *)enumerateAllClasses;
 
 /* 
     Returns an NSEnumerator of ACClass objects.
     For enumerating through all classes, the same as initDefault
 */
 +(NSEnumerator *)enumerateDefaultClasses;
 
 /* 
     Returns an NSEnumerator of ACClass objects.
     For enumerating through all classes as named by the arguments.
     Expects nil for it's last argument.
 */
 +(NSEnumerator *)enumerateClassesNamed:(NSString *)first, ... ;
 
 /* 
     Returns a point cut object as defined by the passed array
     which is assumed to be an array of ACClass objects (each containing some number of ACMethod) objects
 */
 +(ACPointCut *)pointCutWithJoinPoints:(NSArray *)joinPoints;
 
 /* 
     Returns a point cut object for 
 	-all classes as named by NSString objects in the 'classes' array
 	-all methods belonging to such classes, as named by the NSString objects in the 'methods' array
 */
 +(ACPointCut *)pointCutForClasses:(NSArray *)classes andMethods: (NSArray *)methods;




Category:PointlessInformation‏‎

