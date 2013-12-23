---
layout: page
title: ObjectiveCPlusPlus
---




[Topic]

*This page is a . Please help out by linking it to useful information, perhaps gleaned from other places on CocoaDev, perhaps some simple advice from your own experiences with the language.*
ObjectiveCPlusPlus or just ObjectiveC++ (or ObjC++) is the ObjectiveC superset of ANSI-C added to C++ instead.

----

**Tips for use**

ObjC and CPlusPlus are both supersets of C, meaning C code will compile in an ObjC *or* a CPlusPlus compiler (though there are some exceptions for the latter due to reserved words like     class and     new as well as type issues). The nice thing is that the respective syntaxes of Obj-C and C++ do not intersect. That leads the way for Obj-C++, which consolidates all the features of the three languages (albeit with some growing pains evident now and then).

In order to use Objective-C++, name your files with the     .mm filename extension. This will identify the file as Objective-C++ to the compiler, and allow you to mix the languages.

If you're starting with an existing ObjC file, you may have a few problems after you make the change, so beware of new warnings or errors. ObjC++ is a pure superset of C++, so existing C++ files will have no problems. But ObjC++ is not a pure superset of ObjC in exactly the same way that C++ is not a pure superset of C, so you have to be careful of the changes that C++ brings to the table, such as more restrictive automatic conversion rules, function name mangling, etc.

http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/Articles/chapter_12_section_1.html

To enable it, simply rename one or more of your source files <name>.mm instead of <name>.m. This should open the door to all the CPlusPlus features. The downside is slightly slower compile times.

At present there are two problems, one being that you cannot invoke an ObjectiveC method within template code (see PartialSpecialisation for a solution), this is fixed with GCC 3.3 (but you should probably still use the trick from PartialSpecialisation), a new bug introduced in GCC 3.3 is however, that you can no longer store ObjectiveC objects in STL containers, but using a thin wrapper makes it okay (you can even introduce user type converters for this wrapper, and proper reference counting).

If you include old standard C headers in CPlusPlus, then you should do it as #include <cstdio> instead of #include <stdio.h>, both notations work, but the former is the recommended way, don't ask me why.

**The lack of the h is so that the standards folks didn't have to come up with an extension for header files.  Should it be .h, .hxx, .hpp, .H?    The use of 'cstdio' is so that the stdio functions can be added to the std namespace.  Also it lets them separate C header files <cstring> with C++ header files <string> in the cases where there's a name collision**

All STL functions are placed in the std namespace. This means that you either need to prefix each symbol with std::, or import the entire namespace with "using namespace std", this will import the namespace for the current scope (so place it at file level scope to import it for the entire file), you can also import individual symbols e.g. by "using std::random_shuffle".

Importing the entire namespace is not recommended, the reason is that if the standard namespace get new symbols then they may clash with user defined symbols.

If you wish to use STL algorithms with Cocoa collections, then iterators can be found in CocoaSTL.

----

Is there any way possible to have an Objective-C class use an Objective-C++ class? I want to build a framework via Objective-C++ and then use it in normal Objective-C classes. Possible, or no?

----

There is no such thing as an Objective-C++ class; you have Objective-C and C++ classes in an Objective-C++ source file.

Assuming you want to use an Objective-C class in an ObjC++ source file, there's no problem at all. The only restriction is that your .h file must be ObjC clean. This means that you can't use any C++-isms in it, or that if you do you must wrap them all in     #ifdef __cplusplus. The header will be compiled in ObjC mode when it's     #included by a plain ObjC file, so it has to eliminate any C++isms for that situation.

----

So how is this avoided? Using generic void * instead of a specific datatype?

The best solution, if you can manage it, is to have a pointer to a predeclared struct hold all the C++ specific data, and have an obj-c class hold a pointer to said struct. In the header:

    
struct MYClassCore;

@interface MYClass : NSObject
{
struct MYClassCore * core;
}
@end


and in the .mm file:
 
    
struct MYClassCore
{
vector<string> dataX;
map<string, int> dataY;
};

@implementation MYClass
- (id) init
{
core = 0;
if(self = [super init])
    core = new MYClassCore;
return self;
}

- (void) dealloc
{
delete core;
[super dealloc];
}
@end


Why do this?
- Automatically invokes C++ destructors. While this is a XCode option now, it was recently unavailable.
- Completely insulates Obj-C clients of MYClass from C++.
- If you put MYClassCore it's own header you can choose to import MYClass C++ details into Obj-C++ files that need them.
-JeremyJurksztowicz

