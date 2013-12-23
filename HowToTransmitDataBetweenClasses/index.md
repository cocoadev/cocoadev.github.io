---
layout: page
title: HowToTransmitDataBetweenClasses
---

New to Cocoa and to C, I'm unable- after days of trying- to work out how to make the value of a variable available in different parts of a program.

For example (using Project Builder) I define two classes Controller1 and Controller2 each with their own .h and .m files. Controller1 has an instance variable named Variable1 (for example a string or an array), Controller2 has an instance variable Variable2. I want Variable2 to take on the current value of Variable1 (for example in response to an IBAction). How do I communicate the data between the two classes???

I come from Fortran, where there are simple ways to transmit data within a big program, but I think I do understand that the philosopy of Objective C is very different. The principle seems to be that, even if you include the header file of one class in a second class, the second class can't see the first class's variables, only its methods.

So I guess that you're supposed to define an "accessor method" in the first class to transmit the data. But how exactly do you write out this accessor method? And what is the syntax for using this method in the second class- especially, what is its target object?

Ronald Cox

----

This is really more of an Objective-C question than anything else, methinks. Have you read through a good Cocoa or ObjC book yet? If not, perhaps you should do that first. If you know C++, then you already know that member variables of a class are defaulted to being private, which means that only member functions or friend functions of that class can access them. In C (and seemingly in Fortran), structure members are always public, meaning that any function in the entire program can access any member function or member variable within the structure.

Accessors are probably the easiest and most mechanical to write of any function you would ever need. An accessor is usually a one-line function in a class whose sole purpose is to return the value of a variable. So if you have <code>NSString *_myString</code> as one of your member variables in your ObjC class, you accessor's implementation would look something like this:
    
- (NSString *)myString
{
	return _myString;
}


Also remember that other functions/classes in your program may need to modify values of private data members in other classes as well. For this, you need to write mutator functions, which are the exact opposite of accessor functions.
    
- (void)setMyString:(NSString *)newMyString
{
    if (_myString != newMyString)
    {
        id old = [self myString];
        _myString = [newMyString retain];
        [old release];
    }
}


You are probably wondering why my mutator function is so complicated for such a simple purpose, and before you get worried about it, just know that it really isn't complicated at all. All it does is make sure it even needs to reassign the variable, and then if it does, it does so in a way that ensures that the new value is retained and the old one is released, so as not to create memory leaks.

There is actually a really nifty program called Accessorizer that creates accessors, mutators, locking variants of these, and deallocation functions, from any list of variables you give it. I use it all the time in my coding, and it will probably save you lots of time. It's actually what I used to create the above two code snippets for you, because I was too lazy to type it in myself. :-) Just copy/paste your list of member variables into the program, press the "Declaration" button and copy what it gives you into your header file, and then press the "Implementation" button and copy what it gives you into your .m source file.
You can download Accessorizer at http://homepage.mac.com/kcall/accessorizer.html

- MarcWeil

----

Actually, it would probably be better to write all of your accessor methods in the following manner:

    
- (id)foo
{
    return _foo retain] autorelease];
}

- (void)setFoo:(id)anObject
{
    if( ![anObject isEqual:_foo] )
    {
        [_foo autorelease];
        _foo = [anObject retain];
    }
}


Your instance variable should always be prefixed with a _ (underscore), for instance _foo as is the standard convention in [[ObjC. [There are many who don't like the underscore convention.] Creating your instance variables in this manner, and accessing as above will make all of your variables conform to the NSKeyValueCoding protocol... not to mention the above method formats are suggested by the latest Cocoa docs for similar reasons.

Anyway, this probably will not make sense at first, but look into a good ObjC book (as mentioned above), and analyze the above two pieces of code. It will become aparent why things are done this way. You should also check out some good tutorials on ObjC memory management
<http://www.cocoadevcentral.com>
as this is mainly what the above methods aim to ensure.
--EliotSimcoe

----

Marc, Eliot,

Thank you for your quick and very useful replies, including the reference to Accessorizer. I note the different ways of writing the accessor methods and am trying to understand their relation to the problem of memory management. I also agree that I should read a good Objective C book-  I've been trying to guess it all from examples and tutorials on this and other sites and Apple's own docs.

But meanwhile, can I try to spell out a bit more what I think is my main source of confusion - what is the correct destination object for these accessor methods?

In class Controller1 I declare a variable variable1 and accessor methods "setVariable1" and "variable1" (which I've now corrected following your instructions).

To set or access variable1 when I'm coding file Controller1.m, I write

[self setVariable1: aVariable] 

   or 

[self variable1].

Here there's no problem, the target of the messages is "self".

But what is the target object of the message if I want to set or access variable1 from class Controller2? It's not "self".

I've tried and tried different things. In particular, in the files for Controller2, I declare and initialize (I hope correctly) an instance of Controller1 called controller1Object and I write (in Controller2.m):

anInstanceVariableOfController2= [controler1Object   variable1] 

 [controller1Object   setVariable1: anInstanceVariableOfController2]

but no data value seems to be transmitted to or from the textField outlets of Controller1.

I guess my newly created "controller1Object" is useless. It's the Controller1 instance that I created at the beginning of my project (in InterfaceBuilder) that I'm trying to communicate with. How can I make that instance the target of my accessor messages when I'm in class Controller2? Does it have an identifier?

Ronald Cox

----

Your code for creating controller1Object should look something like the following:

    
Controller1 *controller1Object = controller1 alloc] init];


Other than that, it looks like you're doing everything correctly. Are you getting any compiler bugs or warnings? Is anything being printed to the console? Is controller1Object a custom subclass? If so, is your -init method written correctly?

It should look something like this:

    
- (id)init
{
    self = [super init];

    if( self )
    {
        // initialize the class
    }

    return self;
}


Good luck --[[EliotSimcoe

----

Eliot,

Thankyou for that. In reply, Controller1 is an ordinary subclass of NSObject, created in Interface Builder, and with outlets and actions connected to textFields and to NSButtons in an NSWindow. Controller2 likewise, with its own NSWindow containing controls. I was uncertain how to do the init but yes, my init is as you suggest, and with initial test values put in  instance variables.

(By the way, this is just a simple test app I made up, as I postponed my real project, which is to have Controller1 list the titles and parameters of some hundreds of physics experiments in an NSOutlineView, and to have Controller2 plot selected experiments in a custom graphical view- clearly I had to learn first how to make the two Controllers communicate with each other.)

But in fact, quite suddenly I solved my problem of communicating between two Controllers.

I went back to Interface Builder and found that I could pull a connection line between the graphical representations of the Controller1 and Controller2 instances in MainMenu.nib.

So I created an extra IBOutlet for Controller2 called "outletToController1", connected it to Controller1 and I now use this outlet object as my messaging destination, that is my messages from one Controller to the other are now:

    
//getting a value from the other controller
[anInstanceVariableOfController2= [outletToController1  variable1]  

//sending a value to the other controller
[outletToController1   setVariable1: anInstanceVariableOfController2]. 


And this works, the data is transmitted, in both directions.

There doesn't seem to be any Help or Manual for Interface Builder. Although, looking back at Apple's famous (infamous?) CurrencyConverter tutorial, I see now that it does exactly the above, connecting its two custom classes "ConverterController" and "Converter" together by a wire in IB.

In fact, Cocoa and Objective C are incomprehensible to a beginner coming from elsewhere (Fortran in my case). I find myself blocking for hours or days because I've missed some tiny point, desperately looking through examples and tutorials on this site and other sites, trying to find a relevant hint or example. The language is weird for someone coming from purely procedural languages, especially Fortran where (at least in older versions) nothing is dynamic. Would a complete beginner to programming find it easier I wonder?

Ronald Cox.

 ----

Actually, a complete beginner would probably have problems with learning it too. The people that seem to catch onto ObJC and Cocoa most quickly are those that come from a C++ background. Take me for example. I come from a C and C++ background, and I had no trouble learning ObjC in about 6-7 days. It was really easy (especially with Cocoa Browser at my disposal), and the only thing that was really hard to learn was the Cocoa framework itself, simply because it is so huge. I have never seriously programmed in Fortran before, but if knowing it is causing you this much trouble switching to a C-style mindset, then Fortran must be really behind the times, especially because I don't believe there is a language out there that can be used for creating GUI apps that is procedural. Everything is event-based now.

One more thing. The way you described connecting Controller1 and Controller2 is not the optimal way to do what you are trying to do. Remember, you can't make connections across nibs. So of course, there must be other ways to do what you are doing. What I've seen done a lot is that people assign their controller objects as the delegates of their windows, and then they access the controller objects in code by simply calling the "delegate" method for the window.

You'll find that, with Cocoa and ObjC, there are many ways to complete a single task, each of them with pros and cons. That essential freedom might not exist to Fortran.  :-D

Also, remember to subscribe to the cocoa-dev mailing list at http://lists.apple.com/. That is a good reference as well for asking people questions and gleaning information. The archives for this list and another cocoa mailing list are located at http://cocoa.mamasam.com/. Don't bother with Apple's archives as they are useless. And of course, we can't forget the cocoadev chatroom that you can get to from iChat or AIM.

You should also get a book on Cocoa. I would personally recommend two wonderful titles. The first is more of a tutorial book than anything else, and will provide you with a clear and concise introduction to ObjectiveC and Cocoa in everyday terms. It's called *Cocoa Programming for Mac OS X*, by Aaron Hillegass. It will also teach you fundamental Cocoa paradigms and techniques, as well as everything you need to know about the language for a good, solid start. Now, for a very complete reference book on Cocoa, I would recommend *Cocoa Programming* by Scott Anguish et. al. It's quite a large book, but provides a very handy replacement to Apple's documentation. Now of course, Apple's docs, when printed out, take up I think 10 large books, so this book isn't as concise as Apple's, however a lot of the stuff is more conceptual than just raw reference to individual functions, so it is better to figure out how to logically map something out by using this book. The former isn't very expensive, and the latter I believe is in the $50 range. Both can be picked up at most any bookstore.

- MarcWeil

----

Marc,

Sorry for delayed reply- have been away from here for a couple of weeks- thank you for your additional comments and suggestions of where to get Cocoa/Objective C information. I've purchased Aaron Hillegass's book as recommended, and it is indeed very good.

In retrospect, my problem above, as a newcomer to Cocoa/Objective C, was understanding the meaning of multiple instances of a class like a "Controller" Class initially created in Interface Builder. IB creates one instance of that Class, and my program fed data into that particular instance's variables in response to IB Actions. Wanting that data to be available to an instance of a second Class (also a "Controller"), I was naively writing lines for the second Class to allocate and initialise an instance of the first Class, hoping to fetch the data from it. I took quite a while to understand that the latter instance was a new instance with no data, not the one that had been created by IB. I rigged up a connection between the instances of the two classes in IB.

But as you say that solution can't be optimum- it's just a "kludge". I'm far from understanding that- delegates are further on than where I am at present in Hillegass's book!

I feel that this question of communicating data between classes is kind of fundamental.

Ronald Cox.

----

Did you read Apple's introduction to Objective-C? 

-- AndreasMayer

----

More discussion at: UsingAccessorMethodsWithIBOutlets and ClassMethodsAndInstanceVariables

----

Using a NotificationCenter is another good way to transmit data between classes. Especially if those classes have no owner / hierarchal relationship, and only need to tell each other a few things.

----

Also remember that according to some, GettersAndSettersAreEvil. I don't exactly agree (InterfaceBuilder + Key-Value Coding makes GUI a lot easier), but any class that has data should use it on its own. If you are defining a class that only has accessors, mutators, and fields/variables/data members, and none of your getters/setters perform any useful computation beyond memory-leak checks, you should consider using NSMutableDictionary  <DIGRESSION> A really useful Java 5 (1.5?) class is the EnumMap. This allows you to create a fixed map using an enumerated type as the keys. This would allow unique-class-like functionality in a datatype object.</DIGRESSION> If you really need the class-type check, just set an additional object-key pair on the dictionary: class=fooType. Or whatever.

The multi-nib/single-controller problem is a real kludge in Cocoa: it's not possible to have the same object with outlets in two different nibs (unless it's the File's Owner object). This results in very concentrated classes: one controller class in the middle with functions/methods to do everything.

--JediKnil

----

Accessors and mutators are great, but I think it's time to move into 2004 and embrace key-value coding.

Instead of making a -(void)setFoo:(NSString *)s method and calling [someObject setFoo:@"bar"], just make a _foo instance variable and call [someObject setValue:@"bar: forKey:@"foo"]. Don't write an accessor method unless you need to do "extra" stuff when foo is set.

I think a lot of people don't realize that key-value coding does the retain/release stuff for you.

The best line of code is the one you don't have to write! By using key-value coding, and not making -(void)setFoo:(NSString *)s a public API that you publish in the .h file, you've eliminated (1) the code needed to implement the method, and (2) the method declaration you'd normally have to put in the .h file.

----

*Actually, the underscore prefix convention for ivars is deprecated as of 10.3. Apple reserves the underscore for its own use.*

----

Where is this documented?

----

Apple reserving the underscore is documented at [http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html]

As far as a statement about it being 'officially' deprecated, I can't find where I read it, but I think it was on the cocoa-dev mailing list.

