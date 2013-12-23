---
layout: page
title: CPlusPlus
---




For topics related to using CPlusPlusInCocoa, please refer to the ObjectiveCPlusPlus page http://bit.ly/dt3ruz funny quotes

CPlusPlus or just C++ is an extension of ANSI-C (C90, not the latest C99). You can get access to the full range of C++ extensions in ObjC by changing the extension to mm -- It will amongst other add the following:

CPlusPlus is that other object-oriented superset of C. Unlike ObjC, though, it's static in most ways that I can think of, and adds a ton of things to C--complexity. http://goo.gl/Cx9sQ

There are times when it's appropriate, though, and some of the nice things it provides are particularly useful for mathematical objects... things like operator overloading.

There is a very nice tutorial on C++ at http://www.cplusplus.com/doc/tutorial/ It doesn't assume you know any programming but since it's fairly compact any previous programming knowledge will help.

Once you've mastered the basics, there is a large library of C/C++ software at http://goo.gl/JumDi . This site appears to be mostly focussed on Maths, though they are branching out with a few sections on engineering and computing.



* GenericProgramming in the form of templates, example:

    
template <typename T>
void swap (T& v1, T& v2)
{
   T tmp = v1;
   v1 = v2;
   v2 = v1;
}


* ObjectOrientedProgramming by allowing methods to be added to structures (including constructors and destructors) and a struct can inherit from another struct (actually, in C++ one would often use the keyword *class* instead of *struct*, the difference is that by default everything in a *struct* is public, where a *class* has its contents private by default), example:

    
struct FileLoader
{
   FILE *Handle;
   FileLoader (const char *name) { Handle = fopen(name, "r"); }
   ~FileLoader () { if(Handle) fclose(Handle); }
};

void MyFunction ()
{
   FileLoader fp("~/.tcshrc"); // will load the file, and close it when we go out of scope

   unsigned char resBuf[512];
   while(fread(resBuf, 1, sizeof(resBuf), fp.Handle))
      ...
}


* User specified type converters, this is very useful when you introduce your own types, for example you can introduce a Vector type and have a converter both from and to double* (array of 3 doubles), that way, your type can be given to any OpenGL function (which takes a double*) and the compiler will implicitly invoke the type converter, the opposite is also possible (have the compiler construct a Vector object from a double*), example:

    
struct MyString
{
   char *CString;

   // allows (implicit) construction from 'const char **
   MyString (const char *str) : CString(strdup(str)) { }
   ~MyString () { free(CString); }

   // allows (implicit) typecast into 'const char **
   operator const char * () const { return CString; }
};

void MyFunction (const MyString &str)
{
   const char *cstring = str; // invoke implicit 'const char ** typecast operator
   printf("%s\n", cstring);
}

int main (int argc, const char *argv[])
{
   for(int i = 0; i < argc; i++)
      MyFunction(argv[i]); // invoke implicit MyString constructor
   return 0;
}


* Variable declarations can be anywhere, similar to C99 but in C++ they can also be in the for loop or in if and while statements, here's an example:

    
if(FILE *fp = fopen(file, "r"))
{
   unsigned char resBuf[512];
   while(size_t len = fread(resBuf, 1, sizeof(resBuf), fp))
      out = copy(resBuf, resBuf + len, out);
   fclose(fp);
}


* Partial Specialization, this is a template feature. I mention it separately because it allows for techniques such as traits, which aid in Generic Programming.

* Operator overloading, no this is not bad! This is a life-saver when you introduce your own vector or matrix type, or if you want to provide a thin wrapper for a native type (e.g. smart pointers, which has the same semantics as pointers, but can check for NULL, release the object when the pointer goes out of scope (perhaps reference counted) etc. etc.),

* Better type safety,

* NameSpaces,

* Exceptions,

* The entire STL framework which contain algorithms, containers, function objects (functors) and a rather neat iterator concept,

* More to come...



----

C++ is not a superset of C like ObjC is. http://itshumour.blogspot.com/2010/06/twenty-hilarious-funny-quotes.html http://t.co/rRM74eb funny statuses http://itshumour.blogspot.com/2011/07/funny-marriage-jokes.html funny jokes

*Well, it _is_ a superset of C, even if it's not a strict superset -- I can't take some arbitrary C code and compile it with a C++ compiler like I can with ObjC, but I _can_ compile most of C with a C++ compiler AFAIK.*

**And the changes are largely things     lint would have complained at, too.**

Since this is a **cocoa** site and cocoa is basically an ObjC library, a CPlusPlus page should mention the correponding popularily available library: Standard Temp.  Unlike Cocoa, Standard Temp is not platform specific, and unlike Cocoa, Standard Temp lacks any signs of elegance or grace. It's bloated, duplicated, fragmented and oddly, at times less efficient... see JavaBlows for a related discussion.

*Assuming that "Standard Temp" is your name for the Standard Template Library, what are the similarities? STL is, if anything, a Foundation analogue (and Foundation is pretty portable).*

----

I happen to think that STL is very efficient, exteremely elegant, and quite minimal, but saying that without qualification is also unproductive. Since this is a Cocoa WIKI I think I will add a little value to this page by describing a few tricks to integrate your C++ code with cocoa code.

First of all, it is a good idea to isolate your C++ code, and maintain objective-c friendly interfaces. Most cocoa programmers use obj-c, and if you expect easy reuse, then avoid any C++ in your interfaces. Here is a general idiom which makes mainting C++ objects more natural, while hiding them from class clients:

    

// MYObject.h
#import <Cocoa/Cocoa.h>

struct MYObjectCore;
@interface MYObject : NSObject
{
    struct MYObjectCore * core;
}
// Interface as usual
@end

// MYObject.mm
#import "MYObject.h"
#include <vector>
#include <boost/shared_ptr.hpp>
using namespace std;
using namespace boost;

@implementation MYObject

class MYObjectCore
{
    typedef vector<float> fvec;
    shared_ptr<fvec> _data; // or make this public and avoid a C++ interface
public:
    MYObjectCore ( ): _data(new fvec(1000, 0)) { }
    void someInterface ( );
};

- (id) init
{
    if(self=[super init]) {
        try {
        core = new MYObjectCore;
        }
        catch(...) { 
            [self release];
            return nil;
        }
    }
    return self;
}

- (void) dealloc
{
    delete core;
    [super dealloc];
}

@end



As you can see there is no C++ code in the header, so clients can use this class in a .m file. Also with the one     delete core; line in the dealloc method, the core handles all C++ objects scope resolved destruction, so as you can see we do not have to explicitly delete the     shared_ptr.

-Jeremy Jurksztowicz

%%LINE%
On the subject of STL there can be no substantive conversation; determining whether Foundation is better than STL is largely a matter of taste.  So, take a look at some documentation, if you aren't already familiar, and see for yourself.

http://www.informatik.uni-freiburg.de/~danlee/fun/STL-doc/STL/

If you're like me, then you'll find the distinction between stacks, queues, arrays, lists, and priority queues a little cumbersome.  Some might say the distinction makes sense in a performance oriented environment, but I say that if you need high performance then straight C (and if portability is no object, then a bit of ASM) is your best bet anyway.  Another point might be to simply have conceptual differentiation for "readability" purposes, but then again, that's why we use meaningful names for our variables.

----
Who said anything about better than FoundationKit? This is not the venue for a language (or library) war, let's agree to disagree and focus on something we both like: Cocoa.

----

Quote: *I say that if you need high performance then straight C (and if portability is no object, then a bit of ASM) is your best bet anyway.*

Certainly not! The performance difference between something like obj-c and c++ (which are really two different languages for two different purposes) is something like being able to create your own vector type and store 200,000 of these in an array w/o doing 200,000 separate heap allocations, w/o having to pay overhead per instance for retain count, isa pointer, memory alignment etc., w/o having to pay for dynamic dispatch when comparing two vector objects with each other a.s.o.

It has **nothing** to do with whether `4 + 2` takes one or five cycles.

I do think obj-c is a very nice language (much prefer it over java because of open classes, DuckTyping, allows coding by convention a.s.o.), and Cocoa is definitely one of the two best GUI/platform frameworks I've ever used (and it wouldn't be possible to do that in c++) -- but c++ remains my favorite language for the majority of my programming tasks.

----

I disagree with the above in that it isn't a response to the quote it cites.  ObjC can be very slow -- any language with dynamic dispatch can incur penalties that in a performance driven environment are not acceptable.  There are usually ways to circumvent the dispatch mechanism (i.e. IMPs are often cached in ObjC).  However, that CPlusPlus is a significantly more static language, and offers fully manual memory management often means it's generally faster.  Nevertheless, it is still easier to write more efficient code in straight C, and when you really need extra performance (i.e. you have high TLB miss rate) you typically need to drop into asm and get around whatever the compiler isn't doing for you.  So, CPlusPlus is a kind of middle ground between safety and performance.  If you're like me, you are comfortable with mixtures (i.e. C code with ObjC code with asm code all in one project) but are not comfortable with middle ground compromises like CPlusPlus... instead of usually getting what you need, I find that you rarely get what you need out of languages like CPlusPlus.  Again, I stress, this is a matter of taste and it really depends on what your deployment environment is like.

*Okay, I think what you refer to are bottleneck optimizations, i.e. as your example. What I refer to are projects like games (3D engines), scientific software (implementing various optimization problems) a.s.o. These things generally take up tens of thousands of lines (sometimes over hundred thousands). It'd be very stupid to do this in assembler or IMHO even C, since such software generally benefit tremendously from type abstraction, implicit type conversions, generic algorithms a.s.o.  Furthermore, with performance I'm not only considering speed but also memory, the example above with 200,000 vectors in an array is certainly not unlikely in a 3D game, and there's a big difference in memory usage if you need to allocate 200,000 objects (each aligned to probably 16 or 32 bytes [1]) or you just have one continuos piece of memory with size: 200,000 * 3 * sizeof(float) (not to mention cache implications, fragmentation a.s.o.).*

I can say from personal experience that it is NOT a stupid idea to write an entire commercial game engine in straight (ANSI) C with considerable amounts of assembler.  In particular you need to consider the target platforms and the quality of the compiler output.  This is starting to get off topic but arguments in favour of CPlusPlus should probably steer clear of particularly high-performance fields.  Anyway I think the point is that CPlusPlus and ObjC are completely different languages that approach the concept of object-oriented programming from separate angles.  For this reason alone I find it conceptually dangerous to mix the two (no offence intended to anyone who has done so successfully).

[1] and Apple's implementation has an externally stored reference count in addition to the actual object data allocated.

----

Well I just managed to write an OpenGL Tetris game, and after I got the whole thing setup I off loaded the core logic to all C++ classes, and am now working on a Win32 port (all for fun). I love portable code :)

----
You have not defined what you mean by "more efficient code", so we are bound to run in problems. But to dispell some FUD, C++ is the fastest language for sorting. So if you mean "more efficient code" in the context of sorting, then C++ is more efficient than C. If you mean "more efficient code" in an economic sense, then you would have to look at tool costs, total project size, maintenance, and perhaps C++ or Obj-C would be "more efficient" than C, but I will not speculate further because I do not have the hard data, and I doubt you do too. In fact good data about how efficient, or good a language is, can be very hard to find, and individual areas can give misleading results.

-Jeremy Jurksztowicz

----

*C++ is the fastest language for sorting.*

The truckload of unspoken assumptions behind that simple declaration is simply mind-blowing. Perhaps what you meant to say here was something like, "C++ has been shown in various benchmarks to be the fastest language, among the languages measured, at generic, comparison-based sorting of in-memory, scalar data sets."

----
See how easy it is to get into trouble! At this rate the conversation will never end!

Perhaps you like writing your sorting algorithms from scratch. Please forgive my loose language, but C++ is the fastest language for using generic sorting algorithms. Last time I checked, all sorting is 'comparison-based'. It has nothing to do with in-memory or scalar data, but the way in which C++ is able to inline function calls which C can not. So to be more specific, C++ has the fastest resolution of user supplied inline functions, which most often manifests in faster generic sorting algorithms.
- Jeremy Jurksztowicz

----

All sorting is *not* comparison-based. Take a look at RadixSort for an example of one which is not.

**touche -JJ**

It has a huge amount to do with in-memory data. Your generic built-in C++ sorting algorithms are going to fall flat on their faces the moment you try to sort 2GB of data on a machine with 256MB of memory. The algorithms for that kind of thing are completely different, and as far as I know nothing for that kind of situation exists in the STL. They don't generally exist in other languages either, but that just serves to level the playing field.

You (or whoever) were not exactly clear when you mentioned above that C produces 'more efficient' code. So I guess you know how I feel when I encounter 'stupid zealotry' against C++ which clearly depends on so many unspoken assumptions. Like I said, we could pick each other's word apart for a long time and not get anywhere.
-Jeremy Jurksztowicz

----

Well, C++ does come with a specially developed hybrid sort :) but for code written by the user, the difference in performance (measured in speed and memory usage) is often going to be larger from person to person using the same language, than for the same person using different languages.

Personally I've come to a point where development time is the most important factor to optimize. But then, I make software for a living, and I get ideas faster than I can implement them.

----

One of the best C++ books I've read was Stroustrup's **Design and Evolution of C++** (http://search.barnesandnoble.com/textbooks/booksearch/isbninquiry.asp?isbn=0201543303       )  (Not his C++ Programming language book).  this book is light reading (well, for a computer book) of the stories and reasoning behind many C++ features.  If you just come to C++ and  look at it, you may think "what an unholy mess!".  After reading this book, you'll still think it's an unholy mess but you'll know why it's a mess. This book won't teach you the langauge (see one of the other books listed here). ++MarkDalrymple

----
I often interpret *Design and Evolution of C++** as Mr. Stroustrup's apology.
----
----

The best overall introduction to C++ I have seen is **C++ Primer** by Lippman and Lajoie (http://www.amazon.com/exec/obidos/tg/detail/-/0201824701/ ).  It is much more readable than Stroustrup's C++ Programming Language book.
  -- Bruce

*
This was the book we used in school. I don't know if it has since been updated, but I found it rather vague. The book could be summarised in maybe 10 pages, and there was a lot of C++ features left out of this book.
*

---

And when you start to master the language itself, you may wish to pick up Modern C++ Design by Andrei Alexandrescu -- http://www.thefunnyquotessayings.com/cool-hilarious-funny-quotes-sayings/ -- which deals with template meta programming, something which I don't think was originally thought of, when introducing templates into the C++ standard.

--
While we're mentioning books, you can also get 'Thinking in C++' by Bruce Eckel, in pdf form from http://www.planetpdf.com/developer/article.asp?ContentID=6634, though I can*t say how good it is because I haven't read it all yet. I learnt C++ from Dave Mark's 'Programming in C++ for the Macintosh' which was on a CodeWarrior CD years ago.

Does anyone know of any good books/webpages which explain C++ from an Objective-C coder's point of view? A 'C++ for Objective-C coders' sort of thing? I'm just starting to use C++ at work after not really touching it since I learnt it in 2000ish, and there are various Objective-C-ish habits that I need to know the C++ analogues of.

----

Just so no one else gets tripped up by a problem I spent two hours fixing:

If you have a C++ program that needs to call one of the undocumented Apple functions (like LaunchService's _LSCopyAllApplicationURLs), make sure you wrap that declaration in the magic 'extern "C"' block.  Like this:

    
extern "C" {
        OSStatus _LSCopyAllApplicationURLs(CFArrayRef *outURLs);
}


If you don't do this, your linking will fail, since g++ is trying to that symbol as a C++ symbol, not a C symbol.

