---
layout: page
title: JavaBlows
---



Do you mean java beans? *<-- You know, seperate from all the flamebait "discussion" going on here, this is just classic.*

FYI, The only reason this was posted was because someone had JavaBlowsJammedTogether in a discussion and I couldn't keep myself from clicking on the question mark to get rid of it. So to make a long story short I personally don't have anything against Java. 

----

I'm not exactly a Java fan either.
The implementation in OSX seems to be rather ssllooww.
As a language, well, it was fun until I found Obj-C.

----

Actually, Java itself is not bad - in fact, it's rather good. The problem that Java has pretty much always had is that the user interface implementations (Swing, Cocoa, etc.) have always been klunky. The language itself is solid and it's the best thing going (give or take PHP--**Give! Please! I love PHP.**) to implement server (and client) side code. Most of the time when people say something like "Java Blows" they really mean that Java user interfaces blow.

----

IBM has released a good UI library for Java called SWT. It's part of their Eclipse projet and it has been ported to MacOSX. Don't be fooled by the Eclipse surrounding, SWT is available standalone. The link is http://www.eclipse.org
SWT uses native components as much as possible (i.e. if you draw a tree, it's a native tree) so you get the same performance as AWT. However it supports a rich set of widgets, like Swing. Those widgets that are not available on a given platform are emulated through Java code. So essentially you have all the performance of AWT with the rich set of controls from Swing. Sweet. --BenoitMarchal

----

A more modern language might be nice, though.

----

Java as a language is awful -- take something like build an array of the integers 1...5 and then print them in reverse order:
    
Vector a = new Vector();
for(int i = 1; i <= 5; i++)
   a.addObject(new Integer(i));

for(int i = a.length(); i != 0; i--)
{
   Integer obj = (Integer)a.elementAt(i-1);
   System.out.println("Element at index " + i + " is: " + obj.intValue());
}


----

Silly rabbit, if you want to build an array, then do so.

    
int count = 5;
int [] intArray = new int[count];

for (int i = 0; i < count; i++)
    intArray[i] = i;

for (int i = count-1; i >= 0; i--)
    System.out.println("Element at index " + i + " is " + intArray[i]);


Now what is so hard about that?

*This is not a dynamic array... the point was to show how bloated your code gets when you want to work with just semi-advanced data structures like automatically growing arrays.*

Okay - then here's another way to do the same thing in Java, using an automatically growing array. The code is not bloated...

    
List list = new ArrayList(Arrays.asList( new Integer[]{ new Integer(1), new Integer(2), new Integer(3), new Integer(4), new Integer(5)}));
for(int i = list.size() ; i > 0; i--)
    System.out.println("Element at index " + i + " is " + list.get(i));


In Java 1.5, the first line can be replaced with this:
    
List list = new ArrayList(Arrays.asList( new int[]{ 1, 2, 3, 4, 5 } ));//Arrays.asList returns a fixed size list. You require a dynamic list...



Another example showing how bloated Java becomes, is when trying to declare look-up tables statically in the program... try something as simple as this:
    
struct { char ch; char const* name; } table[] =
{
   { '&', "nbsp" },
   { '"', "quot" },
   { '<', "lt"   },
   { 160, "nbsp" },
   ...
};


The Java equivalent is something like:

    
class stupid
{
   stupid (char _ch, string _name)
   {
      ch = _ch;
      name = _name;
   }
   char ch;
   string name;
}

stupid[] table =
{
   new stupid('&', "nbsp"),
   new stupid('"', "quot"),
   new stupid('<', "lt"),
   new stupid(160, "nbsp"),
   ...
};


You gotta love that language...

*And you can't even use a generic find function to search the Java table, as you can with the C version (std::find will work, or std::binary_search, std::lower_bound, std::upper_bound or whatever you desire...*

Ummm... ever heard of Arrays.binarySearch, Arrays.sort? I'm not huge Java zealot, but the above statement is just untrue.

----

Seems to me that you should use one of the more advance Java classes like a HashMap for that table...  Besides the example you give for your statically allocated lookup table forces you to address it with array access and to check the value of each item to find which item your trying to get in the struct. Unless of course you implemented a few lookup functions to hide that, but then if you do that, your bloated.... If you use the correct Java datastructure, then you have a easy solution.  

and to initialize it you can do this:

    
myHM = new HashMap();
myHM.put("&", "nbsp");
myHM.put("\"", "quot");
....


then  when you need to get something it's this:
    
myHM.get("&");


so it's basically the same.... and I don't need some specialized sturct....

*The intent of the example was to show the missing capability of Java to easily store data tables within source code. The example might be for a lookup procedure, and so, a hash-map could do instead, but this was not my intent, and it would only work for very few cases, imagine we write an applet with 5 questions, each with 3 possible answers -- as a programmer we would probably like to just write this in a compact way in the source, but here we can't use the hash-map -- and don't tell me to store the questions as an external XML file and use Java's XML parser or similar... Java is really not for compact code... I have still not seen anything written in Java where the C++ counterpart wasn't at least as short (often much shorter).*

So let's demonstrate how to create a data table in Java, and to do so compactly. Yes, Java eschews the stack in favor of the heap. Precious little is on the stack. So we get to avoid issues with memory addresses being no longer valid because the stack has been popped. Yes it's a tradeoff. I can type, though. A few extra characters don't mean much.

    
Map map = Maps.ArrayToMap( new Object[]{ new Character('&'),  
         "nbsp", new Character('\*), "quot", new Character('<'), "lt", new Integer(160), "nbsp"});
*

And yes, no such thing as Maps.ArrayToMap exists. I'd have to write it. Pretty straightforward, though.

    
class Maps
{
    static Map ArrayToMap( Object[] keyValuePairs )
    {
        if( keyValuePairs.length % 2 != 0 )
            throw new IllegalArgumentException( "The last key is missing it's value pair" );
        Map *map = new HashMap();
        for( int i = 0; i < keyValuePairs; i+=2 )
        {
            map.put( keyValuePairs[i], keyValuePairs[i+1]);
        }
        return map;
    }
}



Java 1.5 will allow this:
    
Map map = Maps.ArrayToMap( new Object[]{ '&',  "nbsp", '\*, "quot", '<', "lt", 160, "nbsp"});



*I think the statement that �I don't need some specialized struct� is interesting.     struct is a keyword to introduce new datatypes to the compiler, and this is worth a lot, e.g. if you write a mathematical application you may need a new vector type, matrix type or similar, if you write business logic you may need monetary types or whatever. C++ allows you to easily introduce new types and make them behave exactly as the built-in types (supporting implicit conversions, infix operators etc.) -- Java OTOH does not allow for new primitive types, it only allows for classes, which are generally more work and does not support the same features as primitive types.*

Generally more work? If all you need is a struct, this is perfectly valid Java:

    

class Struct
{
    public char aChar;
    public String name;
} 



It's a type, you can treat it just as a struct:

    

Struct a = new Struct();
a.aChar = '&';
a.name = "nbsp";



All of the complaints on this page about 'bloat' comes from Java's preference of the heap over the stack. Yes, it makes initialization static things a bit more tedious. It makes memory management a breeze. I do static initialization (regardless of language) much less often than I do memory management in C++

----

*This is probably one of the reasons that Java code often gets bloated, because it has to use classes instead of types. If we compare it with ML instead, here we can declare a new datatype representing a binary tree as simple as the following.*
    
datatype 'a tree of br('a tree * 'a * 'a tree) | lf;

*Let's also write a find function which returns wether or not an element is a member of the tree.*
    
fun find n lf = false
  | find n br(left, v, right) =
      if n < v find n left
      else if n > v find n right
      else true;
*

Umm... classes *instead of* types? Classes ARE types. Just as they are in C++.
----

Quoting Alexander Stepanov http://www.stlport.org/resources/StepanovUSA.html

*You can't write a generic max() in Java that takes two arguments of some type and has a return value of that same type. Inheritance and interfaces don't help. And if they cannot implement max or swap or linear search, what chances do they have to implement really complex stuff?*

----

*I believe Java 1.5's generics capabilities allow this. Regardless, I don't buy this argument as a whole*

After reading the whole article at the link above I came to two conclusions. First, I wish this guy was teaching my classes on set theory and proofs. Having rich tangibles to spark ones interest would have done me wonders in the way of motivation. Second, simply because he didn't rag on Objective-C doesn't mean it's any better in the context of what he doesn't like about higher level languages. 

----

He is attending a workshop at my former university this summer, and I honestly don't remember when I was as excited about something as when to get a chance to meet him! He is one of my biggest idols ;-)

About your second point: As I see it, ObjectiveCee is for GUI programming and inter-app communication (i.e. exchanging "data" between applications) � for everything else it sucks! But for this subdomain it actually has some advantages, i.e. keeping methods as their own type (allowing things like the responder chain), categories (extend objects that other people instantiate), forward unsupported methods (allowing class-posing, decorators, etc.), introspection (take something like the keybinding dictionary, which is just a list of methods, where the responder is asked at runtime for support of the action method) etc. etc.

And none of these features exist in Java ;-)

Btw: I have written iterators, functors and some helper functions to be used with ObjectiveCee, allowing collections to be used with normal STL algorithms, and functors to easily be constructed from selectors � if there is any interest, I can try to wrap it up in a nice package and put it online (I don't know how many Cocoa programmers actually use STL?).

----

Generic functions are being added to Java in the upcoming 1.5 release, and look to me to be cleaner than the C++ template mechanism, if not quite as flexible (but it loses the automatic C++ object code bloat associated with templates too, so hooray).  One of the things I dig about Java is that Sun knows how Java sucks and is working to fix its problems; too many of the standards body languages are willing to lose suckage in 10-year rather than 1-year intervals.  -- Bo

----

No, 1.5 does not get any new functionality with respect to generic code. It just gets rid of the typecasts.
So in the code above you would declare the type as Vector<Integer> and save the typecast applied to the result of elementAt(), and that is basically all.

And you wouldn't be able to do e.g. Vector<bool>. But autoboxing (as they call it) tries to remedy this situation.

----

To say that Java is bad because it's cumbersome to print a list of integers in reverse order is like saying that a dishwasher is bad, since it won't wash my clothes. Get real. -- TheoHultberg/Iconara

----

No it is not � it might be like saying that a hammer is bad because it cannot crush an egg though. Something you most likely won't do, but if you cannot do it, then you probably can't use it for hammering nails into the wall.

----

*About your second point: As I see it, ObjectiveCee is for GUI programming and inter-app communication (i.e. exchanging "data" between applications) � for everything else it sucks!*

ObjectiveCee is also good for publishing robust object-oriented interfaces (API / SPI), such as framework and plugin API. You can add methods to frameworks and libraries without invalidating the interface; unlike C++ where you have to recompile all clients if the methods change (and why C++ is better off statically linked). (Note: you still can't change the number of instance variables in an ObjC object -- just methods). ObjC's dynamic lookup mechanism makes loading, querying, and running plugins relatively easy.

-- MikeTrent

Computer languages blow. All of 'em. ObjC is nice, but it blows. Java may be nice--though personally I don't think so--but it blows. C++ also blows. Haskell would blow if it were more popular. Even Smalltalk and Lisp, beautiful though they are, blow.

The tradeoffs in blowiness is kind of hard to itemize at this time of night, but you can sometimes say things like "ObjC blows for down-to-Earth mathematical structures" or "Java blows for anything I want to do with it."

-- RobRix (who wonders why we need this page. Isn't it obvious that everything blows?)

Yeah.  On some level, every language blows.  I sure as hell wouldn't use Objective-C to write enterprise business logic, nor as a server-side language, but those happen to be the few areas where Java doesn't blow.  Client-side Java definitely blows, though.  C++ blows at everything, etc.  --OwenAnderson

----

Object-orientation blows: http://www.paulgraham.com/noop.html - presents the interesting and (to me) extremely plausible idea that OO is popular because it lets mediocre programmers (of which there are many) do many small tasks.  In practice (at a large telecoms company that shall remain nameless) I've found that OO techniques are generally used to "repair" old code, not by fixing the code, but simply by isolating it.  This allows (promotes?) a sort of continual-spaghetti effect, it's just broken up into easily-digestible chunks.

Speaking of everything blowing, how relevant will this discussion be in a hundred years? http://www.paulgraham.com/hundred.html

----

I once sat in a seminar where the speaker made the claim that C was too abstract!! He felt that asm was better. What next, promote Turing machines as the ultimate technology? It's easy to say that OO blows. It's easy to say anything blows. OO promotes a continual-spaghetti effect????? OO promotes duplication???? 

*
I'd suggest you actually read the article before reacting.  It actually goes the opposite way of what you assume, and says that more abstraction is desirable.  The author is, in fact, a huge proponent of Lisp because of the abstraction it provides.

The second article makes the point that common data structures are simply special-case efficient implementations of general lists, and raises the rhetorical question as to whether maintaining structural distinctions between them (as compared to merely functional distinction) will be necessary or desirable in the future.  Again, the point is that more abstraction and elegance is desirable and inevitable as languages progress.  I certainly don't recall anything saying we should go back to programming by setting dip switches.

As for the observations on object-oriented practice, they're not just mine, and the truth ought to be obvious to anyone: just because it's object-oriented doesn't make it good.  Bad programmers (which outnumber the good ones) write, at best, mediocre code.  Object orientation allows the bad effects of these bad programmers to be isolated from one another, which is why it's such a big deal in industry, where even good programmers aren't always given the time to do the job right.  That's all the observation was meant to indicate.

And as for duplication, it might be a facetious example, but how many times have you written or seen something like this while doing OO work:
    
for (int i = 0, i < N, i++)


And finally, yes, it's easy to say that anything blows, which is exactly why it's done on **this page**.  For an interesting question on why blowing may not be particularly **bad**, see the following:
http://www.dreamsongs.com/WorseIsBetter.html (the bit where the author argues against himself is entertaining)
*


I read an article where Microsoft is attempting to move away from OO and move toward attributed data and message based communication. Could someone explain how attributes and messaging are not OO? I just don't see how XML goes against the grain of OO. 

Those who get off on saying that their lower level language is better than another higher level language are silly. Most hard-core Cocoa developers will program closer to the metal when need be. That's the beauty of Objective-C. You are not tied down to any set way of doing things. Personally, nothing beats Cocoa for creating a graphical user interface and nothing beats C for developing data manipulation tools. If you throw in CHUD and force yourself to learn how to hand tune bottlenecks in your code, you can do just about anything with Objective-C. 

Just remember, once you link against a library that uses data structures you are essentially programming with object orientation. 

*That statement belies a serious misunderstanding of how object-orientation works.  Maybe it's just poorly worded, but are you implying that using data structures makes things OO?  Or linking libraries?  Either one can be done in ways that will make an OO guru lose his lunch.*

**Belies**?? *I think you mean, "betrays".* Freud would call this a parapraxis. When misunderstanding belies insight, then
argument is futile.

----

Believe it or not, any amount of abstraction can still be generalized. Objects are just glorified structures with rules. How many grains of sand does a mound make? When does a structure become opaque? When do functions operating on a structure become a specialized group of functions operating on a structure? When does this approach turn into �objects� interacting with other objects? Taking this concept to a higher level does not isolate it from the underlying functionality. That was the point I was trying to get across. 

*
presents the interesting and (to me) extremely plausible idea that OO is popular because it lets mediocre programmers do many small tasks.
*
 
Yes, novice Cocoa programmers and script kiddies are empowered by technologies that are easy to understand. So this is how you justify saying object-orientation blows? What a narrow view!! I find it amusing that some programmers feel a need to idealize their convictions. 

----

*"I find it amusing that some programmers feel a need to idealize their convictions."*

You're attributing a sentiment that really isn't there; I use OO all the time and like it well enough - if I didn't, I wouldn't hang around a Cocoa wiki.  This doesn't prevent me from seeing how it *can be used* in TheRealWorld.  Any number of well-thought-of books (Antipatterns spring to mind, in fact, the whole patterns field arose from trying to avoid OO-related blowiness) have documented this, and all of them will tell you the same basic truth: no matter how good the tool, some people will use it poorly.  The ways that things are used poorly will vary from tool to tool, and this, in the context of this page is "blow".  Java blows for static tables (above).  Perl blows for readability.  Lisp blows for efficiency.  Prolog blows for string handling.  One way in which OO blows is that it mitigates sloppy design and implementation.  These aren't faults of novices and script kiddies, they're mistakes made all over the place by any number of people for a variety of different reasons, poor scheduling, dealing with legacy code, lack of coffee that day, and so on.  If you've never been unfortunate enough to end up on a project like that, it's because you're lucky, not because OO is somehow perfect.

Now back to Worse being Better; if OO blows because it mitigates the effect of sloppy work, is that really that bad?  Consider that you can have just one or two sloppy bits in a large system, but because they're isolated, the system as a whole can function perfectly well.  Other paradigms don't tend to isolate bad effects quite as efficiently.  Sounds like a strength now.  In all seriousness, that set of links on Worse is Better is really an informative read for OO in general.

Finally, I'd say that if someone **doesn't** know why OO (or Prolog or Fortran or Lisp or **any** tool they're using) blows, then they're probably in for a whole heap of trouble.

*I agree completely. Know your tools, know why and where they blow, and avoid using them in those ways!*

----
Boy, I never thought that when I made the comment Java blows, way back when, that it would be turned into this.

*I was the one who joined the two words together. It's an interesting experiment to see how a wiki can work from such a simple thing. See http://c2.com/cgi/wiki?PromptingStatement --StevenNewton *

----

I find it easy to think in terms of tradeoff curves - you can trade performance for flexibility, speed for memory, etc. Just a case of visualizing that curve and working out where you want to be on it. These days I don't really mind trading performance because computers today are so damn fast and because I don't really need the performance. If I were to start writing games then I would probably be more inclined to write more code if it made things smoother. There are lots of dimensions other than performance and succinctness to these curves, like portability, maintainability, readability, etc. -- MikeAmy

----

*Even the best tool cannot compensate for an inept user. Even if all they have to do is push a button, chances are they'll figure out a way to push it counter-productively.* � BrentGulanowski

