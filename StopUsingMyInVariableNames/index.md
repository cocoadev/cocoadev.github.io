---
layout: page
title: StopUsingMyInVariableNames
---



Some people use a "my"-prefix for variable, function, and typedef names, believing it a good convention for keeping their code separate from other people's code ("it's MY variable, not YOUR variable"). Other people believe the "my"-prefix is an abomination and should be eradicated from the Earth. Discuss Below:

----

**Pro "my" side **

I see Mike's point about finding a better variable name than myVariable when actually writing the code in a REAL program.  HOWEVER, when teaching how to use C or giving example code, it is much clearer to use the myVariable format.  This is because, by its nature, a piece of sample code should be generic and should not try to specify a particular use for the variable.

For example, is this declaration...
    
General/NSRect myRect;

or is this declaration...
    
General/NSRect line;

clearer to someone who is learning how to use General/NSRect for the first time?  I can imagine him saying, "Where does 'line' come in when I am trying to declare a rectangle?" [ what's wrong with "lineRect"? That would be the superior choice -- General/MikeTrent ]

Now, if we are talking about putting the declaration in your REAL code, sure, use the most appropriate name you can and avoid myRect.  But when explaining code, I think it is a good convention to use the myVariable format, exactly for the reason that it is generic.  The newbie will then know to change this variable name to his own more appropriate name when he adds it to his program.

Also, Apple uses General/MyController as a sample class that you create in their documentation.  O'Reilly uses myVariables in their books Learning Cocoa and Building Cocoa Applications.  Hillegass even uses a myArray in his book Cocoa Programming for Mac OS X.  So, the professionals are using it to teach, too.   Just my two cents.  -- Alexander

I use the the 'my' prefix with frameworks such as JAVA/AWT and .NET, because the system classes do not have any prefix, so often the most appropriate name is reserved by the system (this is only for instance variables, to whom I apply title case and thus get the naming conflict) --General/AllanOdgaard

----

**Con "my" side **

I believe "my" is bad because it tells you nothing about the variable. Every time you use "my" in a variable name, there will be a better choice based on how the variable will be used. For example:

    
General/NSRect myRect = General/NSMakeRect(0, 0, 10, 1);
General/NSRectFill(myRect);


Could be:

    
General/NSRect frame = General/NSMakeRect(0, 0, 10, 1);
General/NSRectFill(frame);


or:

    
General/NSRect lineRect = General/NSMakeRect(0, 0, 10, 1);
General/NSRectFill(lineRect);


I agree the examples above are a bit contrived, but you get the idea. So in essense I believe the "my" prefix basically means "I'm too lazy to write good code".

-- General/MikeTrent

----

If the 'my' were attributed to the name of another object, that also had 'my' then it would make more sense, but otherwise its just going to be confusing for yourself and someone else looking at the code later. -- General/MatPeterson

----

myVariable is better for sample code. In the example given above,
    
General/NSRect lineRect;

the variable name makes no sense. If the purpose of the example is to illustrate how General/NSRect works, the correct choice is to call the variable myRect.

----

I agree that myVar is ok for examples. I use myVar all the time when I post here, but I rarely us my* in real code. I think the better thing to discuss is the merits of self documenting code. I used to use short abbreviations or acronyms for variables to save time, but I found that I save more time using long self descriptive variable names than spending the time to comment stuff out. If you get proficient enough with the text editor you code with, using long self descriptive variable names is a non issue thanks to copy and paste (double clicking on a variable name in PB's text editor has to be the action I perform the most aside from typing). 

For example, if you have a variable that tells you the highest row selected in a table, I used to think that this was quick and an efficient:

    

    int topSel;



But all it takes is a couple of months to pass to forget what "topSel" is supposed to represent. One way to avoid ambiguity would be to document the definition of "topSel" with a comment, but this process is time consuming. I do believe documenting your code with comments is the best practice to get into, but it's impractical to cut and paste a comment everywhere the variable is being used. This is why I think that self documenting coding practices are worthy of mention here. 

I think the best solution for the variable name in this case would be to use a self documenting name like:

    

    int topRowIndexOfSelection;



By double clicking on the long variable name in PB's text editor, you can copy and paste long variable names almost as fast as typing a short abbreviated one, and don't forget the plus side. You are essentially embedding a comment everywhere a self descriptive variable name is used. 

Suggesting that lineRect is better than myRect is ok, but you're missing out on the chance to instill a better coding practice. "lineRect" doesn't tell you any more about the rect than myRect does when read out of context, which is exactly what happens when you come back to code that hasn't been looked at in a long time. 

Just my two cents worth --zootbobbalu

----

See Martin Fowler's "Refactoring" book.  Ideally code should document itself.

----

If you're holding a reference to an object so as to avoid the overhead of calling it's accessor (as with General/[NSUserDefaults standardUserDefaults]), then myUserDefaults counts as self-documenting code.

----

"I agree that myVar is ok for examples" I disagree because examples teach people how to program. By using "my" we are saying "It's ok to use 'my' in your program". Similarly, if our example leaked memory and didn't bother checking for errors we are saying "It's ok to leak memory and not check for errors in your program." People who write code for others to emulate should always "do the right thing".

"Suggesting that lineRect is better than myRect is ok, but you're missing out on the chance to instill a better coding practice". The point is not to use the "line" prefix, or the "Rect" suffix, the point is to avoid relying on meaningless conventions to convey information. If you have something better than "lineRect" or "myRect" in mind, then for goodness sake use it! WRT "topRowIndexOfSelection", I think that's fantastic and wholeheartedly approve.

"'my<object>' counts as self documenting code." That convention is not used by everyone. By assuming 'my<object>'  always describes an unretained reference to an object it may be harder to find memory leaks in your (or other people's) programs. In this context, "my" is meaningless: you should always be aware of an object's "owner" and how the object is supposed to be used no matter what it's name. I would not call this a good example of self documenting code.

-- General/MikeTrent

----

I don't see why this has to be a black or white issue.  Cleary, the "my" prefix is unfit for many cases when a logical description of the variable can be embedded in its title.  But this isn't always the case, and often a variable exists on such a temporary, placeholding level that no variable name is a compelling alternative to the "my" convention.  I don't see the practice as confusing or misleading in itself, so much as something that can be damaging when used without care.

I disagree that it's a teaching convention that condones bad code practices.  Most programmers learning at that level have a knowledge not suited to learning why good code conventions pay off.  It's not like error detection, which has a salient effect on the end product; it's conventions for the purpose of readability and ability to share code with others.  How many early programmers are really that concerned with this, and should we really expect them to be concerned at all?

-- General/IanArcher

*"Most programmers learning at that level have a knowledge not suited to learning why good conventions pay off."* - that's why you teach them, so that they do learn it. Positive reinforcement, bro.

----

My intent was not to say that coding conventions are entirely inappropriate for new learners, just that many of the benefits of good conventions -- especially those strictly for readability and code sharing -- are subtle and not easily made evident to a new learner.  Exposing new learners to "good practices" when they're simply trying to learn how to use an General/NSRect might be giving them information they don't have the maturity to appreciate and ultimately absorb.  The distinction between what constitutes necessary convention at this level is a hard one to make, but I think categorizing the "my" prefix into the "Universal Bad"s overshoots the mark a bit.

By the "No My's" argument, we could also argue to ban using "i" in for loop iteration.  Yet, is the following code any more readable:
    
for (int temporaryIterationInteger = 0; temporaryIterationInteger < MY_MAX; temporaryIterationIngeter++) /* ... */;
?

The answer is likely no; the author can quickly see the context of the canonical "i" and understand its use.  Similarly, the following snippet:

    
// resize our frame by 100x200
General/NSRect myRect = [self frame];
myRect.size.width += 100;
myRect.size.height += 200;
[self setFrame:myRect];


would be no more readable with any other variable name.  Do I think "my" variables are good general practice?  No; I just find the universal deprecation of their use a bit pedantic and inflexible.

-- General/IanArcher

----

Hmm.  I would disagree with the above in several respects:

(1) A new learner has no way to distinguish between something that is a best practice and something that was done for demonstration purposes, and is likely to ape an example in all respects.

(2) I have never seen a case where "my" was helpful as a variable prefix.  In contrast, I almost always use i for simple iterations.  It has a well-acknowledged meaning, and there is no need to distract the reader with a long variable name.  Of course for more complicated cases longer names are in order.  I won't say "more descriptive" because i is a throughly descriptive variable name, in some contexts.

----
    
class General/SuperRect
  {
  public:
    General/SuperRect(int x, int y, int width, int height) :
      myX(x), myY(y), myWidth(width), myHeight(height)
      {
      }
  };

Well. Now you have.

Actually, I think an initializer list with x(x) and y(y) will work, I would never do such a thing.
----

If the point is to write good code in examples, then not using "MY" does not extend to not using "i".

----

I disagree that "i" is a thoroughly descriptive variable name, when -- as you argue to "my" convention's fault -- it is thrust into a new learner's tutorial.  Your case in 2 is valid, but I think it applies equally well to the "my" convention.  "my" variables are ubiquitous in coding tutorials; few experienced programmers would come across it used "appropriately" and be confused as to its use.  It's hard to make the case that "i" is absolutely right while "my" is absolutely wrong; it's simply a matter of "how likely is it that this will confuse readers".  I think neither "i" nor "my", when used for small temporary variables, instruction, etc, present a strong case for prohibition in this light.

As for (1), I don't think the cause for alarm is strong here.  Variable naming conventions are a very natural way for a coder to organize relationships; a task I think any intermediate coder will intuitively come into on his own.

-- General/IanArcher

----

'temporaryIterationInteger' is a ridiculous example of an alternative to 'i'. I don't like using variables with non-descriptive names like 'i' or 'my...', but I would not call such a variable 'temporaryIterationInteger', rather I'd give it a name which is relevant to what it actually represents. Like 'cakeIndex' if looping through an array of cakes (I'm hungry!) or 'buildingNumber' if you have an array of information about buildings which are referred to by number. In the latter case the name makes it clear that the variable has some meaning rather than being an arbitrary index needed only for looping, so you might want to use it for something else with it inside the loop.

A psychologist might read something into this frequent use of 'i' and 'my' ;) 

-- General/AngelaBrett

----

I don't know ... I prefer 'disposableLocallyScopedIntegerForIterationPurposesOnly'. But then, I have a 30" display ...   (No, I don't. But I *dream* that I do.) Of course, this makes one wonder whether 'disposableLocallyScopedIntegerForIterationPurposesOnly++;' makes sense. In this case, it should probably be 'disposableLocallyScopedIntegerForIterationPurposesOnly + 1;' You know, to help the beginner.

*but that has no effect? it should be     disposableLocallyScopedIntegerForIterationPurposesOnly = disposableLocallyScopedIntegerForIterationPurposesOnly + 1 should it not?*

Not for beginners - that's too much information. Gotta' leave enough room for descriptive variable names! ;-)

*Personally, I think people should stop being so greedy and use '    your' as a prefix in example code ;)*

----

*A psychologist might read something into this frequent use of 'i' and 'my' ;) *

A psychiatrist, maybe...

*But I *dream* that I do*

Would you like to **talk** about your dream a little bit more...?
And they say that size DOES matter. But sometimes a 30' display is just a 30" display.
At other times, it's exhibitionism.

*Well, actually, in my case it's really REALLY bad eyes. (and frikin' HUGE fonts)*

**...and you know what a reputed cause of REALLY bad eyesight is...**

----
It is called "Hungarian Notation".

An extreme can be found in any Microsoft code. A variable in MFC would be named something like "lpcstrName". Of course, no one outside of Microsoft thinks this level of detail is a good idea.

However, in moderation, it can be extremely useful. Typically, the "my" prefix is used for object instance variables to distinguish them from local variable. Similarly, "our" is used for class variables (General/ObjC doesn't support have them) to distinguish them from instance and local variables. It is also common to use "m_" and "sm_" for the same purpose, respectively.

It is better to get into the habit of doing this now so you don't have to bo back and do it later and possibly screw something up.

Just because something is "standard industry practice" doesn't necessarily mean it is a good idea. But in this case, it is.

----

both the phrases "standard industry practice" and "deviant sexual practice" give me the willies, because they imply that there is somebody out there qualified to identify standard practice, or that such authority asserts that somehow because standard practice is general, it is optimal.

----
That is why I put it in quotes. I will try a couple of definitions to clarify...

standard industry practice - A practice that, if not followed, will reflect negatively on one during a technical review meeting.

good idea - A standard industry practice that one follows even when the boss isn't looking
----

I love it!

----

My two cents (which in $US amounts to, well, a bucket of sand)...

Typically you will never see "my" in "real code" (definition of real: code you see in the industry -- subdefinition: code that YOU see in the industry that NO ONE else does). Unless the letters "M" and "Y" mean something, they are rather pointless. Hell, it is hard enough to get people to use MEANINGFUL variable names! How many times have we seen "temp" followed by a definition for "temp1" followed by its close (but not kissing, except in VA) cousin, "temp2"? The Cat in the Hat Lives! (Soon to be a Disney "The Cat in the Hat Lives on Ice"... a guy can dream, can't he?)

Back to the topic. "My" Examples: good. Fine. Comments would be FAR better, but heck, use "my" in an example.

And whatever you do, don't touch myBeer.

General/LloydSargent


----
Long time ago I used to write variable names where local variables started with a or an, as in aRect, and passed in variables started with the, as in theRect.
