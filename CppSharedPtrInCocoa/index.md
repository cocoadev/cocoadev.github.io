---
layout: page
title: CppSharedPtrInCocoa
---



*Discussion moved here because it did not fit on the CPlusPlusInCocoa page.*

Here is how you can use the excellent boost::shared_ptr class to manage scope for your objective-C objects, or just to adapt to a C++ interface. A template specialization of shared_ptr  can eliminate the redundant reference count, and improve performance, but is not necessary for basic use.
-JeremyJurksztowicz

    
// Cpp Files.
#include <boost/shared_ptr.hpp>

// Cocoa Files.
#import <Foundation/Foundation.h>

////////////////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////////////////
namespace ObjectiveC
{
	void release (id obj) { [obj release]; }
}

int main (int argc, char * const argv[]) 
{
	using namespace std;
	using namespace boost;
	NSAutoreleasePool * arp = [[NSAutoreleasePool alloc] init];
	{	
		NSAutoreleasePool * subarp = [[NSAutoreleasePool alloc] init];
		
		NSString * aString = [NSString stringWithString:@"Some string data."];
		shared_ptr<NSObject> aStringRef([aString retain], ObjectiveC::release);
		shared_ptr<NSObject> anotherStringRef = aStringRef;
		aStringRef.reset();
		
		[subarp release];
		
		NSLog(@"%@", *anotherStringRef);		// Output class.
		NSLog(@"%@", anotherStringRef.get());	// Output instance.
		
		anotherStringRef.reset();
		
		NSLog(@"%@", anotherStringRef.get());	// Should be nil.
	}
	[arp release];
	return 0;
}


----
Stay far, far away from any sort of shared pointer. Just because it is part of boost doesn't mean it is a good idea. This will bite you hard - but only after you've written several (hundred?) thousands of lines of code. Reference counted pointer are, IMHO, the worst part of Objective-C. 

C++ places an emphasis on the stack as opposed to the heap. The STL passes parameters by value on purpose. Use copies. Put your objects on the stack. Avoid pointers at all times. If you must use them, use an auto_ptr. If you must use pointers and can't use auto_ptr, pay attention and do it correctly. But remember, this is a last resort. Any usage of pointers requires explicit justification in comments.

For me, Objective-C (and Microsoft .NET and Borland's Delphi on Windows) is nothing more than the latest funky way to build user interfaces. Write real code in good C++ using templates, the STL, and only the minimum of pointer logic and polymorphism.

----
You seriously need to explain this sentiment. First you say that shared_ptr will bite you in the ass, then say that Cocoa's reference counting scheme is poor. I agree with the latter, which is why I propose using shared_ptr! Please tell me your alternative! The above code is working VERY well for me and I no longer have to worry about retain counts going wild (at least in my obj-c++ code). For the record, boost's shared pointer reference counting is being used NOT cocoa's, so if you confuse the two then you could be in trouble (but not necessarily). (writing a template specialization that uses cocoa's reference count is pretty easy if this distinction confuses you, or if it becomes inconvenient {I can think of a few fringe cases [well they are fringe to me!]} <I swear, no more parenthesis!>). -JeremyJurksztowicz


----
The sentiment that pointers are evil? I thought by now one wouldn't have to defend such a statement.

I was not talking about the relative merits of different strategies of reference counting. Reference counting itself is a bad idea. Pointers are a bad idea. C++ provides alternative ways to manage data.

I say one should write good, cross-platform code using C++ with a minimum of pointers. Then, do your user interface and OS interaction in Objective-C, or even Objective-C++.
----
Pardon the sarcasm, but have you noticed that ALL OBJECTIVE-C OBJECTS ARE USED VIA POINTERS!?
Furthermore, reference counting is a bad idea compared to what... If you have to use pointers (like in Cocoa) then you have to manage pointer lifetime. I see three alternatives: Manual pointer management (BAD), reference counting (BETTER), garbage collection (BEST, [not always, but mostly]). The biggest problem with Objective-C reference counting, that of manual retain/release calls, is handled by the above shared_ptr template. Once you create a shared_ptr<id>, you can treat it like any automatic variable (int, double, structs) and it will be deallocated when the last reference leaves scope. You are aware that C++ stack unwinding is present in Objective-C++, right?

Now tell me one more thing. How am I to use runtime polymorphism without using pointers in C++?
Don't get me wrong, I am 100% against bald pointers, my current project has > 80,000 lines with over 200 calls to new and only one call to delete. Almost all of the memory is handled by shared_ptr and my program runs for days on end with no memory leaks. No offence, but it sounds like you are confused about reference counting in C++. The only major hazards are circular references (use boost::weak_ptr for that) and memory fragmentation (and only if your app runs for long). If you can substantiate your claim that reference counting is bad in general, or tell me an alternative way to use polymorphism (virtual functions), then I can try to refute some specifics, until then I am afraid you are spewing pure FUD. - JeremyJurksztowicz

----
I must admit, now you are beginning to amuse me.

Yes. I am aware that all Objective-c objects are pointers. That is why I advise people to limit their usage of Objective-C to just the user interface and (maybe) OS interaction. Most of the lower-level OS routines have a C-based Core Foundation interface. However, you are quite correct, Cocoa does require pointers and Objective-C.

Pointers are not inherently evil. They are just hard to use and error prone. With C++ (or Objective-C++) there are 4 alternatives.

* Manual Pointer Management. Yes, pointer management is hard and I would prefer not to do it. But if you must use pointers (as in Cocoa) this is the best. Pay attention, document, and know what you are doing.
* Reference Counting. A disaster waiting to happen. It sounds like you have managed it OK so far with small projects. I wouldn't risk it on a project 30 times larger.
* Garbage Collection. Off-topic. If you think GC is the answer, move to a Java forum. This one is about C++ in Cocoa.
* Use C++. Use the stack. Pass parameters by value. Create containers of values. Use pointers only when you must. When your system is fully functional (and only then), look for bottlenecks and optimize.


I don't really like Cocoa's reference counting. The autorelease pools, the implicit retains, it is all way too "mushy" for my taste. When I write for Cocoa I have to pay a lot of attention and make sure I am doing it right. That is the only correct way to program with pointers anyway. In my non-UI code, which needs to be portable to .NET, etc., I use C++ where I don't have to worry about the details.

If you want to do runtime polymorphism, use a real OO language like Objective-C. C++ is a funky hybrid. Once you get a few million likes of polymorphic C++ code you will see just how bad it can be. For C++, use just a little bit of polymorphic behavior, a few templates, and the rest is just plain-old well-structured C. If you are writing C++ any other way you will probably give up on it at some point and move to Java, C#, or something else and go through this all over again.
----

I am glad I amuse you. Now can you tell me some SPECIFIC PROBLEMS with reference counting? All you have written is conjecture and unsupported opinion. Your statement about not using polymorphism in C++ is cryptic, and betrays a bad attitude to the 'funky hybrid' language. I would like to know which multi-million line projects you worked on with C++. Did you ever consider that ALL multi-million line projects can succumb to bad design and bad management? 

You suggest to use a little bit of polymorphism in C++, still without suggesting how I can do this without pointers. If you are confusing massive object heirarchy design (everything derives from a     class Object) with polymorphism, then I can understand. C++ is not well suited to such monolithic heirarchies, Objective-C is. In a way I am following your vague suggestion of using 'a little bit' of polymorphism and mostly relying on the stack already. Most competent C++ programmers do this. Your suggestion that large projects in C++ which use polymorphism are doomed to failure also betays a profound lack of knowledge. The industry I am entering (audio software development) has many multi-million line programs using C++ polymorphism. Even without using smart pointers (a shame IMHO) these applications manage to get by fine, and are maintained and enhanced for years (decade + in some cases). And in these cases C++ cannot easily be replaced because of it's amenability to real-time performance, which many modern languages lack. **cough**Java**cough**. Even more pertinant is the fact that these applications have dynamic plugins which rely in polymorphism to work (VST plugins (c++), AudioUnits (c++), RTAS(c++), etc...).

For the record: I agree that using pointer should be limited to where necessary only, but your arguments are very academic and do not help any programmers in the real-world of imperfect tools. This is a Cocoa WIKI. I share your low opinion of Objective-C reference counting, which is why I suggest a safer alternative     shared_ptr. If you want to have an academic discussion concerning the design of current languages, great, there are plenty of message boards for that. I posted the above code to solve real problems I encountered, in the hope of helping real practicing programmers solve similar problems.

Finally, If I am to use 'a little bit of polymorphic behavior' in C++, should I use bald pointers, or reference counted pointers, or perhaps you have an alternative suggestion? I await a specific suggestion as to how to solve a real-world programming problem, that does not rely on 'Switch to language X'.

- JeremyJurksztowicz

----
This is one of the few forums I read on CocoaDev because it is specifically about using C++ with Cocoa. So I think discussions about C++ memory management are appropriate for this particular part of CocoaDev.

I'm not quit a fan of Cocoa. It is much better than the old Toolbox, much better than MacApp, and much better than MFC. From my twisted point of view, Cocoa and .NET are very, very similar. I wish things were better, but they aren't, and they are much better than they used to be, so I'm not going to complain about Cocoa that much. I am very happy that Apple created Objective-C++. I seem to be one of the few people that really use Objective-C++. (I also like using .NET with C++.) As far as APIs go, I like the Carbon API more. It fits into C++ better. But for User Interfaces, you must go with what your vendor gives you. Apple prefers Cocoa for that, and so do I.

I think the best example I can give is the iostream part of the C++ standard library. It is very efficient and very much template-based. It is very amenable to iterator-centric design. It has just enough polymorphism, IMO. When I am writing my own iostreams, internally, there are no pointers at all. But the interface I present to the external world is that of std::istream * and std::streambuf *.

I can't give quantifiable data and objective evidence. These are just my opinions after watching the evolution of C++ and I think they are perfectly appropriate for this forum. I certainly can't argue with the idea that all multi-million line system can succumb to bad design and bad management. I would say that such projects are the natural result of bad design and bad management.

I'll try to state my point of view a little more clearly.

Pointers are difficult, powerful, and dangerous. It is fine to use them, but any such use should be justified. You need to explore other alternatives that might be available. In C++, there are such alternatives. One of the things I try to do is tell people about these alternatives because, quite frankly, very few people know about them. I wasn't attacking your use of shared_ptr, I was trying to make people aware that there are options in C++.

As far as Cocoa goes, I'm not quite comfortable with the Objective-C pointer management. There are lots of side-effects. Objective-C pointer are NOT like C pointers due to the reference counting. I'm just not ready to wrap them in a shared_ptr and forget about it. It seems like it adds a layer of complexity to something already too complex for my tastes.
----

I think we are in almost complete agreement, I just jumped on the statement that reference counting is a bad idea in general, because there are few clear alternatives when a pointer to an object is NEEDED (which is sometimes the case). In these cases shared_ptr is exceptionally useful, and very difficult to 'mess up'. That is, other than circular references, there are few opportunities for novices and experts alike to abuse shared_ptr. Otherwise, I cannot pick out something specific you have said which I disagree with. Perhaps we work in very different domains, where a technique which is suitable in one, is depracted in the other. I think I mistook your forceful suggestion of alternatives (specifically using the stack *almost* exclusively, which I agree with by the way) for a denunciation of smart pointers in general. When pointers are necessary (which they are for me and my plugin-happy apps) use a wrapper (auto_ptr or shared_ptr or Loki::SmartPtr), I think that such a statement is fairly easy to agree on.

As for using shared_ptr with Cocoa objects, I use it succesfully, and with no problems. If something which you investigate seems too complex for your needs, then you are probably right. After all, only you know what you need to do the job. If others deem that their needs can be satisfied by the shared_ptr code above, then they should only be discouraged by specific flaws presented clearly, not a general 'bad feeling'  about reference counting. Happy programming, let your errors be compile-time and your exceptions few.

-JeremyJurksztowicz

----
Thanks so much for this recipe!  I had been wondering if using the automatic reference counting of shared_ptr was possible as an alternative to Cocoa/obj-c's manual reference counting.  I'm an experienced C++ programmer getting into iPhone apps, and I love the ease of use and foolproof interface that shared_ptr provides.  Listening to Cocoa devs talking about hunting down memory leaks because they forgot to manually decrement a ref count somewhere in their program made my jaw drop: "you mean you don't have shared_ptr?" I asked. Because in C++ we figured this out a while ago and it's in the C++0x / TR1 standard now!

As for the ignorant commenter above who thinks pointers are to be avoided at all cost, you're ridiculous.  You can't just use the stack everywhere and make copies all the time; that's inefficient and impossible in many cases (I'm guessing you try to just create everything you need in the stack of main()?).  Pointers (and the idea of allocating / managing memory) have been around for decades, and all the software from your kernel on down use objects allocated on the heap, whether in C, C++, or obj-C.  The idea that generations of programmers shouldn't have been using "dangerous" pointers is absurd, and your suggested best practices sound like an awful idea � I find it hard to believe you have ever worked at a company or on an open source project that used C/C++.  You completely miss the point that shared_ptr automates the refcounting/management tasks involved in using allocated objects, and makes pointers altogether safer & less bug-prone.  If pointers make you uncomfortable, you really should look into using shared_ptr � they take over the task of having to deallocate your objects, and make sure that pointers used in a complicated data structure can't accidentally get deleted when they're still needed.

-cce

