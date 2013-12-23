---
layout: page
title: CPlusPlusInCocoa
---




Q1: How do I incorporate a CPlusPlus class file into a Cocoa project? I tried the Play Thru sample, but as  soon as I include the header file in the Cocoa project file, I get errors on the first 3 lines of the class declaration in the header file. ('class' is an error, '{' is an error, '}' is an error) 

----

Is the file being compiled as ObjectiveCPlusPlus? Try changing the extension to .mm. If you take C++ and stick it in a .mm file, it should compile to exactly the same code. If you take Objective-C and stick it in a .mm file, it should compile to almost identical code, with the only change being mangling of function name symbols.

----

Q2: Can you call an Objective-C++ class from Objective-C? I'm about to start on a port of an MFC class and am a little confused on what can and cannot be done.

----

There is no such thing as "an Objective-C++" class. You have Objective-C classes and C++ classes. What ObjectiveC lets you do is mix the two types of classes in the same source file, that's all. The limitation is that if you have an Objective-C class whose header contains C++ code (or vice versa), then every class that #imports it must also be compiled as Objective-C++. If you can avoid any C++isms in your header, then you can #import it in pure Objective-C files without problems.

Something that should be obvious but isn't always is that any use of C++ in a header file will "contaminate" everything that #imports that header, forcing you to use ObjC++ for all of the files that use that class. A mixed ObjC/ObjC++ approach can be annoying because of this.

----

This is indeed most annoying. It is also seriously exacerbated by the fact that the Obj-C compiler rejects      extern "C++" {...}  which would allow you to wrap C++ function prototypes or header includes much more easily. Unless I'm just missing something (I always get a "parse error before string constant" for any such attempt). There surely must be a simple way to call external C++ functions from an Obj-C class? This is super simple - no C++ ivars or mixed classes, just some external code that happens to be C++. --GC.

----

**On incorporating a C++ class as an instance variable**

One thing that's very important and can be annoying is that you can't incorporate a C++ class as an instance variable, like this:

    
@interface MyClass : SuperClass {
   std::string name;
}


I believe the compiler lets this through, but the ObjectiveC object creation process will *not* call the ivar's constructor! Likewise, the object's destructor won't be called when the "parent" object is deallocated. If you want to use C++ objects as instance variables, then they must be done as pointers. This is because, in ObjectiveC, a class is created by allocating a chunk of memory equal to the sum of its instance variables' sizes. It doesn't individually create each variable. Since ObjectiveC is built on top of C, the idea of "constructors" simply doesn't exist.

I would probably just do something like this:
    
@interface MyClass : NSObject {
   std::string* name;
}
@end
@implementation MyClass {
   - (id)init {
      name = new std::string;
   }
   - (void)dealloc {
      delete name;
   }
}


----
You could call the ObjC's constructors directly; be aware that you're doing something essentially evil, though -- especially, be sure to call the destructors directly later

----
This is out of date as of Tiger.  When running on Mac OS X 10.4 and later, using GCC 4.0 only, you can pass the *-fobjc-call-cxx-cdtors* option to the compiler (there is a corresponding build setting for this option in Xcode) and the Objective-C runtime will be certain to call the zero-argument constructor and destructor of a C++ object that's declared as an instance variable of an object upon that object's instantiation and deallocation.  Note that this *requires* the Mac OS X 10.4 version of the Objective-C runtime; you can't compile this code on Mac OS X 10.4 and run it on Mac OS X 10.3.9, because the 10.3.9 runtime doesn't include the feature.

----

**Performance discussion** 

Q3: Does anyone use Objective-C++? I am starting a project that would benefit greatly from C++ standard library. I've heard that compilation times are increased, and portability is near nil, but experience is the best teacher. I have a fair amount of experience with both languages, and feel comfortable using them independently. Are there hazards that I have not anticipated?

----

Don't be surprised to see your compile times triple.  ObjectiveC++ is slow to compile and slow to run... which I think is the compiler's fault since C++ doesn't have a runtime AFAIK.

----

Do you have any proof or measurements of your "slow to run" claim? It sounds highly unlikely.

----

Slow to compile yes. That's a good excuse to go get that G5 you always wanted. I think the 'slow to run' is just random bogosity.  There is no additional jazz the compile really has to do, since the Objective-C message sends are really just function calls.  There would be no impact on the C++ side of the world.

----

I dispute the "random bogosity" claim.  The few times I've needed to depend on Objective-C++, I saw some unusually slow performance.  "Porting" the code almost verbatim to Objective-C from C++ cut my execution time in the relevant sections of code in half.  ObjectiveC code should be a tad slower than C++, and I stress that it was a brain dead port, so I attribute the significant speed boost to a performance bug in GCC when it encounters Objective-C++... if you know for certain what's going on, please let us know.

----

If you take code and totally change it and stick it in a .mm file, and it's slow, then that doesn't mean Objective-C++ is slow, it means your code is slow.

I've used Objective-C++ extensively and had no performance problems due to the compiler misbehaving. There is also no technical reason why Objective-C++ would be slower, since it's just a very thin layer on top of the C++ compiler.

----

It's still pretty theoretical until someone starts citing benchmarks and posting code for said benchmarks. The above noise consists only of rhetoric.
And we all know how *clear cut* are the answers provided by benchmarking. Theoretical indeed.

Personally, my very subjective impression is that CLI code in C++ *does* take a bit longer to compile than equivalently-complicated C code.
Criticism of the runtime performance begs for benchmarks.

Whether or not one may find incorporating C++ classes more or less efficient depends on the need for extensive use of production code.
Furthermore, the jury is apparently out on whether brain dead ports of C++ code to ObjC is the rule rather than the exception.

----

As a heavy C++ user, I can tell you that the only thing that makes C++ slow to compile is templates. So, if you #include <iostream>, your compile will slow down. If you throw in your own templates, things will get slower. 

----
There's a misunderstanding here - ObjectiveC++ is the language that people are saying is slow to compile.

----
However, if compile time is your only consideration, you really should stick to Perl. Heavy use of C++ function templates can create very fast, very succinct code. If you know how to use templates, you can write 5-10 times less code than someone who doesn't. It will be hard to get the syntax right, and the compilation will be really, really slow. But you will be very unlikely to have any logic errors and your code will run really, really fast.

I saw a posting a while back from someone claiming that Java was faster than C++. I took his C++ code, re-wrote it in C++ and soundly trounced the Java code. But I discovered something interesting. Sometimes, the C++ was actually faster than "regular" C. One benchmark did lots of matrix multiplication. I changed the pointers to arrays to operator overloading that returned matrix objects on the stack. In theory, that should have been very slow. It was actually much faster than the C version.

Use C++ function templates. They are very fast and powerful. Don't create temporaries like this:

    
std::string getString(void)
  {
  std::string test("Hello");

  return test;
  }


Use C++'s optimized function results.

    
std::string getString(void)
  {
  return std::string("Hello");
  }


----

Just to pre-empt C++ haters, I have to do a lot of HEAVY searching and sorting operations, which C++ is far better at (and a bit faster) than straight C. 

----

Is there a downside to unnecessarily "compiling a class as Objective-C++"?  How can you even tell the difference?  Also, if you'll forgive me since I've never used it, I can't think why there would be any C++ in the header for an Objective-C class (or vice versa).  Wouldn't the mixed stuff be in the implementation?

----
Compiling as Objective-C++ has two downsides. First, ObjC++ is significantly slower to compile (not to run!) compared to ObjC. Second, C++ is not a pure superset of C, so your code may break. In particular, C++ is much stricter about implicit casting, and it adds keywords which could conflict with variable or function names.

There are two cases where you might have C++ in a header. One is for instance variables, and the other is for method arguments/return types. Example:
    
@interface ObjCppClass : SuperClass {
   SomeCppClass *class; // a pointer, so it doesn't hit the constructor/destructor problem
}

- (AnotherCppClass)method:(ThirdCppClass);
@end


Particularly if you only have C++ classes in instance variables, there's no reason you couldn't use this class from pure ObjC code, but you won't be able to compile the header because of the C++ in the header. It's possible to move ivars into a struct which is only visible from the .m file, but this is more complicated.

----
For instance variables, all the (objective-C) compiler needs to know is how much space to allocate for instance variable.  So you ought to be able to fix it by not importing the header that declares     SomeCppClass in the header file, and instead using     @class SomeCppClass;.   Does that work?

I suppose that for arguments you could again pass things by pointer, and use an @class declaration in the header..

----
You're going to really confuse the compiler by telling it that SomeCppClass is an ObjC class in the header, and then changing your mind and declaring it as a C++ class in the implementation. I don't think that will work.

----
As another thought: perhaps you could factor the methods that require C++ out into a category, and only declare the category when using ObjectiveCPlusPlus?

----

There are a couple more points relevant to ObjectiveCPlusPlus in headers: Regular CLanguage files can't import the header (CPlusPlus function names work differently than regular C language ones), and ObjectiveC++ is even less portable than regular ObjectiveC. It can't be compiled with anything other than Apple's GCC.

----
True, though if you're using cocoa your portability is already essentially nil...

----
GNUStep exists and is quite usable. Using ObjC++ kills any possibility of using it, at least in the near future.

----
But hardly anyone has GNUStep installed, and in the limited experience I've had with GNUStep there were plenty of bugs to work around.  Certainly enough that I would expect to have to devote real time to trying to make a cocoa app work on GNUStep.


----
Define pure CPlusPlus. If you aren't doing any ObjectiveC messages or fancy dynamic stuff, then you already are in pure c/cpp.

But you have the right idea. Do your interface in ObjectiveCPlusPlus and do your heavy calculations in C++. In this sense, ObjectiveCPlusPlus is no different from any other GUI interface. You pretty much have to give Aqua/MFC/VCL/etc. what it wants. Do your own code, however you want.

----
And on that note, would the implementation of multithreading as seen in http://cocoadevcentral.com/articles/000061.php still work in the case of ObjectiveC++?

----
Yes. There is no difference. I wanted more power and flexibility than NSLock and NSConditionLock, so I wrote my own locking classes in C++ using PThreads. Specifically, I wanted a Guard lock (similar concept to auto_ptr) and a SignalLock (like in Win32). I'll re-do my above example with a guard lock:

    
- (void) print: (char *) msg
  {
  try
    {
    // Using C++ and a Mutex guard, I don't have to worry about
    // unlocking.
    MutexGuard guard(m_lock);

    if(!msg)
      {
      std::cout << "No message to print!" << std::endl;
      return;
      }

    std::cout << msg << std::endl;
    }
  catch(std::exception & err)
    {
    std::cout << err.what() << std::endl;
    }
  catch(...)
    {
    std::cout << "Unknown error!" << std::endl;
    }
  }


----

I'm developing a Cocoa app that uses some C++ libraries (no choice there - these are proprietary libraries that provide functionality that can't be provided by any other libraries).  Part of the development is creating a C++ class to handle events send by classes in the libraries.  I want this C++ class to then call my Cocoa code to deal with GUI things.  But I'm struggling to figure out how to call the Obj-c methods from my C++ class.  I have a reference to the Obj-c class, but I can't import the header file of any Obj-c classes in my C++ class (obviously).  But I also can't make my C++ event sink an Obj-C++ class, because the headers that I need to import for the libraries are not written with Obj-C++ in mind - that is, they have reserved Obj-C words as types and variables and the like (like id).

I can make the C++ class aware of the Obj-C class by just declaring in my C++ header file:
    
class MYObjCClass;

I still can't compile because I still haven't told the C++ class what methods are available in my Obj-C class.

so... I created an ObjC++ class (MyGoBetween), that has header and class files defined in C++ syntax.  This class has a reference to my ObjC class, and my C++ class has a reference to MYGoBetween.  Because MYGoBetween has a C++ header, my C++ class can import it, and know what functions are available in the ObjC++ class.  The implementation, being ObjC++, can send normal messages to ObjC classes.

----

I have c++ and ObjC classes that dont have a problem talking to each other. Can you edit your c++ class that is handling your events? If you can, did you try importing the Cocoa.h header? This will force you to compile this as an ObjC++ file, but as long as you dont have to include this in a pure C++ file that should be fine. If this is all true you can call ObjC methods from your C++ class. This would eliminate your go between which sounds like a good thing to me. If your confused on how this is working dont worry, it takes a while but once you get it its not that bad. -- 

----
Just wanted to add that Camino the mozilla browser ( http://www.caminobrowser.org ) uses Objective-C++ to link its cocoa interface with the Gecko underpinnings. It works great with no trouble what I know. Camino is free software, so if examples are needed, it's easy to look here. 

    WebKit is, as all should know, also ObjectiveC++.

----
Curtis- The problem that I had with that is that I can't compile my event handling class as ObjC++.  Some of the headers for the libraries that the event handler needs to reference include some code that's not valid for ObjC++ (using the obj-c reserved word "id" in several places for example).

----

Just a little utility class to help you take advantage of <algorithm> with your objects which provide a NSEnumerator. It is quite incomplete, and I really don't like the operator == right now, but it is simple, and works well. I am working on a c++ class which will take selectors and arguments and wrap them in a c++ function object (like boost::function), so that you can do stuff like this: 
    std::find_if(ObjCIter([myArray objectEnumerator]), ObjCIter(nil), ObjCFunctor(@selector(isKindOfClass:), [NSString class]));

    
class ObjCIter {
	NSEnumerator *_enum;
	id _temp;
	
public:
	ObjCIter (NSEnumerator * e) : _enum(e), _temp(nil) 
	{ if(_enum) _temp = [_enum nextObject]; }
	
	// No point in having a non const version as const id is not really relevant.
	id operator -> ( ) const	{ return _temp; }
	
	id operator *  ( ) const	{ return _temp; }
	
	id operator ++ ( )		{ _temp = [_enum nextObject]; return _temp; }
	
	id operator ++ (int)		{ id t = _temp; _temp = [_enum nextObject]; return t; }
	
	// TODO: Still not sure how to implement this properly.
	bool operator == (ObjCIter const& oci) const { return _temp == oci._temp; }
	
	bool operator != (ObjCIter const& oci) const { return _temp != oci._temp; }
};

namespace std {
template<> struct iterator_traits<ObjCIter> {
	typedef std::forward_iterator_tag iterator_category;
	typedef id value_type;
	typedef unsigned difference_type;
	typedef id pointer;
	typedef id reference;
};
} // END namespace std


Jeremy Jurkztowicz (arketype (_AT_) myrealbox (_DOT_) com)

ADDENDUM: Wow. I just looked at CocoaSTL, really cool. One less library for me to write :)

----

I have an instance of a C++ class as an ivar for an Objective-C class, and I need the C++ class to call back to the Objective-C class. Do I need a global function outside of the Objective-C @implementation? If so, how exactly would I reference that from the C++ class, with or without a user defined callback function? Hope this is clear.

----

If you're compiling the C++ class as Objective-C++, then you can simply do [obj message] as usual.

If you aren't, then pass the class a function pointer and a context variable, like most callbacks, and then pass it a function which treats the context variable as an object and sends it a message. Something like this:

    
// C++
typedef (void)(*MyCallbackFPtr)(int special, void *ctx);

void MyClass::SetCallback(MyCallbackFPtr  fptr, void *ctx) { ... }

// to call it
   callback(42, mCtx);

// ObjC
void MsgSendCallback(int special, void *ctx) {
   [(id)ctx someMethodWithInt:special];
}

// ...
   cppMyClassInstance->setCallback(MsgSendCallback, self);


----

I am trying to make use of some open source C++ code in my Cocoa project. The code in question is completely defined by its headers, which come as .h files. If I include these files in my own, I get over a thousand errors. After reading the info at the top of this page, I changed the files from .h to .mm files, but I get exactly the same errors. The compiler is barfing on the very first C++ thing it comes across, which is the 'namespace' keyword. What's the trick? --GrahamCox

----

OK, I made some progress - I changed the file that uses this code to .mm and that's fine, the code compiles. However, there's still one puzzle. I can't include the C++ headers in my Objective-C header, only in my Objective-C implementation file. In this case I might be able to work with that, but what if I needed stuff in my header? --GC

----

Then every single file which includes your header must be .mm as well. Welcome to the wonderful world of the C preprocessor. Alternatively, if the header will work for pure C/ObjC files without the C++ stuff, you can surround the C++ syntax with     #ifdef __cplusplus blocks.

