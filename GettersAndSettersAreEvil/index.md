---
layout: page
title: GettersAndSettersAreEvil
---



In a sister-article to ExtendsIsEvil, Allen Holub [http://www.holub.com/] explains why getter and setter methods are evil, too:

http://www.javaworld.com/javaworld/jw-09-2003/jw-0905-toolbox.html
http://www.javaworld.com/javaworld/jw-01-2004/jw-0102-toolbox.html (a follow-up, with design guidelines)

Something to consider while playing around with KeyValueCoding, and JavaBeans.

I hope this will be an equally interesting discussion as ExtendsIsEvil. The introduction to the article also further explains some of the controvercies in ExtendsIsEvil.

--TheoHultberg/Iconara

*Also discussed at: http://c2.com/cgi/wiki?AccessorsAreEvil *

And also mentioned in the interview with BjarneStroustrup, although his conclusion may differ from that of the Java world: http://www.artima.com/intv/goldilocks2.html

*[...] I particularly dislike classes with a lot of get and set functions. That is often an indication that it shouldn't have been a class in the first place.  It's just a data structure. And if it really is a data structure, make it a data structure.*

ChrisEccles :  Hear, Hear !  There is no point in subclassing if all you want is a repository for data.  Long live the NSMutableDictionary.

----

Maybe this is valid in some context, but I call shenanigans on it. It is very much the case, all over my code, that I need to change the state of some mechanism. Changing the state will require magic to be done behind the scenes. Take for example toggling a drawing mode of an object in an open gl ( or some other kind of ) visualization. For performance reasons, you may have some kind of cached version of the object in it's last state ( display list, for example ) -- in changing the state, you need to build a new display list. if you simply have      object->drawWireframe = false , the next  time you call     object->draw() it will have NO WAY of knowing somebody changed its state. Unless it keeps a variable like     wasWireframeLastTimeDrawn which leads to a maintainability nightmare.

Now, I understand the argument being made, and it's certainly got merit in certain circumstances, but a blanket statement like "Is Evil" is like saying there's only OneTrueWay to indent code. As far as I'm concerned, anybody who makes such blanket statements is an ignoramus, regardless of his or her credentials. And now, you can call *me* an ignoramus, for making a blanket statement about ignorami.

--ShamylZakariya

*ShamylZakariya: You may be a misunderstanding of Allen Holub point. He is not suggesting you access instance variables directly in place of accessor methods - direct access is even eviler (if there is such a word). Instead he is suggesting that no object take apart another object and change its values, but it should ask the other object to do it. -- RyanBates*

----

**First Article:** I really like this article. It clearly defines how object-oriented programming is designed to work. Rather then creating dumb data objects with getters and setters, give them responsibilities that relate to their data. Sometimes this is a lot easier said then done, but it makes a lot of sense.

However, I do find it difficult to give up accessor methods all together. The main reason is that Objective-C does not have garbage collection, and therefore it would be very difficult to manage the memory without them. An alternative is to make them "private" methods (not defining them in the header file).

I also find it difficult to live without accessors when doing UnitTesting because I need to check if the object behaved correctly with its data. Anyone have a solution to this? Perhaps I'm doing UnitTesting incorrectly.

I was a little puzzled by the UI section in the first article. Although he did have a point, I think it is more important to encapsulate the View from the Model then it is to encapsulate one object from another. He sounded as if it would be better to place the business logic and UI in the same object - here I disagree.

**Second Article:** This article explained his UI approach in more detail and brings up some interesting points. He states that MVC is old and no longer applies, but instead he offers, what I would consider, MVC at a different perspective. The core separation of the view from the model is still there, but the controller and model layers are united.

I think he is missing one key point, and that is to keep the responsibilities of an object small so the object stays small. A model object has far too many responsibilities if it has to manage both the business logic and keep the UI up to date. Certainly you can delegate the work on to other objects, but then it turns into a controller object and you are back to the MVC paradigm.

Back to accessor methods. I see the importance of data abstraction, but if a method simply returns data, does that mean it is an accessor method? For example, if one is creating an e-mail client which contains a Message class, is it wrong to have a "- (NSString *)message" method in the Message? We don't care how it stores it's message, it may retrieve it from a file , generate it, or even store it as an NSString instance variable - but all we want is the message. Is this wrong?

Perhaps the proper question is: Why do we want the message? It could be to display it to the user, send it over the network, or save it to a file. If we try to place all these responsibilities into a single Message object, it will be doing too much work.

Overall these are two very interesting articles and I am looking forward to the discussion. -- RyanBates

----

*it would be very difficult to manage the memory without them*

How do you do memory management, if you use getters and setters? It seems a very odd argument to me. I have never used any setters in memory management, but I may misunderstand what you are getting at.


*I also find it difficult to live without accessors when doing UnitTesting*

If you are testing functionality that does not exist, you are testing the wrong thing. If you do not have accessor methods, you don't need to test them.


*but if a method simply returns data, does that mean it is an accessor method*

Yes and no, as mentioned in the first article it's quite ok to return things, but you should not reveal the implementation, that's the point. So in your case, with a -message method, you should definitely avoid it. Instead you could use an extractor class (as shown in the second article).

It should be noted that Allen Holub is a very seasoned developer, and what he writes is not to be taken lightly. It is based on many years of experience on object oriented design. You cannot say that it is nonsense. You may want to go to his webpage [http://www.holub.com/] and look up the article linked to next to the article on ExtendsIsEvil. 

-- TheoHultberg/Iconara

----

*How do you do memory management, if you use getters and setters?*

Like this. Otherwise, whoever is changing the value of deliveryDate will need to worry about retaining and releasing.

    
- (void)setDeliveryDate:(NSDate *)newDate
{
	[deliveryDate release];
	deliveryDate = [newDate copy];
}

- (NSDate *)deliveryDate
{
	return deliveryDate;
}


*If you are testing functionality that does not exist, you are testing the wrong thing. If you do not have accessor methods, you don't need to test them.*

I am not referring to testing accessors, but testing how other methods in the object alter the data. Because the unit test is a separate object, the only way it can access the object's data is through an accessor method.

-- RyanBates

----

The memory management bit is in the set method, and is only needed because it is a set method, if you don't have it, you won't have the problem. If you do have an accessor method, you can retain your object, no problem, but you should consider: do I really need this method? does it violate encapsulation?. It's the same as the unit test argument: if you don't have the functionality, there is no problem.

Your answer clarifies what you mean about needing accessors for unit testing, and I think you're doing it the wrong way, or rather, a bit too much. If no part of the code ever touches the instance variables (and nothing should), you can't use them to check the state of your object, that would be pointless. Unit tests should be used to see if the object or component reacts to input in the way it should, so to test how a method changes the internal structure of the object, test also the methods that uses that internal structure to do something else. 

Your unit tests need not know about the implementation specifics, and even shouldn't! Consider the XP method of first writing the test and then the implementation, the test cannot know about the implementation. If you have a correct test you could change the internals in every way you want, since it's not important how it does things, but that it does whatever it does.

--TheoHultberg/Iconara

----

I hate to keep going on this tangent, but perhaps I can explain the UnitTesting problem better through an example. I'll build off the Money example at the beginning of the second article. As it stated, getValue() is an accessor and is bad, so let's use increaseBy(amount) instead. How do we unit test the increaseBy(amount) method? In order to see if it functioned properly we need to retrieve the value of the money object to see if it increased, but we must have an accessor to do this. -- RyanBates

----

A valid counter argument, and it's impossible to answer without knowing the complete implementation of the Money-class. To correctly test the increaseBy(amount) method we need to know how the information is used later. 

One way would be to implement an exporter class to work with the test case, as on page 3 of the article. It would be valid if that is the way that the amount is retrieved and displayed to the user (but in that case, via a different exporter). However, I can agree that it would be quite tedious just to do testing.

--TheoHultberg/Iconara

----
I'm not very good at reading natural language, so I may have missed a significant part of the point.  Perosnally, I thought there was only one real lesson, repeated throughout the three pages; namely, don't pass ivars out for processing, if you can process them in-house.  It's a very, very, very important point; and simple (and reasonable and sane) as it may sound I see hundreds of lines of code everyday, written by coders with years of expereince under their belt (or wherever they put expereince) that doesn't conform to this basic principle of OO design.  Code that outsources ivar processing results in leap frog spaghetti.  The rest of the article, I think, tried to explain why experienced programmers may outsource their ivars.  Aside from proceedural habits -- which shouldn't be overlooked -- the odd hack way in which many programmers code (code now, ask questions later) results in class interfaces that aren't designed (at write time) to do what they end up doing.  It's hard to just imagine the ways in which a class is going to fit with the rest of the architecture, and I strongly believe that UML, or even some good ole' pen and paper would considerably improve code use foresight... (I personally like pen and paper and OmniGraffle for projects that contain less than 50k lines of code)
----

So I have to say that I don't really get this article.  As in Ryan's example above, it seems like to not have an accessor for the money just unnecessarily complicates at least the unit testing, and probably everything.  Anyway, it seems to me like there were two main objections often voiced in the comments that Holub did not address.

1)  Getters and setters are better than raw data access because they are regulated by the class owning the data.  The object can (and I'm sure all of our objects do) take actions to ensure the internal consistency of the object.

2)  Getters and setters don't necessarily expose implementation because they can perform calculations to bring the internal representation for the data inline with the external model.  

The 'feel' that I have is that often accessors allow for looser coupling of objects than a deluge of specialized methods.  Maybe.  I don't know.

----

The point is that if there are certain operations you know are going to be performed on the data when returned from the object, you might as well tell the object to do it for you.  This is better factoring, follows OnceAndOnlyOnce, and is a sign of good design.  It also is what I glean to be the core concept of that thing about containers... what's it called again? *Encapsulation?*  If you know what it is you know what I mean, if you don't this is what they're trying to say anyways.  Classes should be responsible for manipulating their own things.  There is similar thinking in the EiffelLanguage about having either "procedure" methods that change things or "function" methods that return values, but no methods that change things and return values.... maybe I'm getting off topic.

*Yes, and how about if you don't know, or want to pretend you don't know what other classes may or may not want to do?  Including an accessor definitely increases flexibility.  Plus, if there's some specialized sequence of operations that needs to be done in one place, it may make more sense to keep that sequence where it's actually used than in the other class.  As far as encapsulation.. there's no difference between an "accessor" and any other kind of method as far as encapsulation, both are just part of the interface.  The internal data model may or may not correspond to the accessors.*

----

*Classes should be responsible for manipulating their own things.*

It's as simple as that, isn't it. Egad! I can't believe the amount of breeze a nutcase like Holub can inspire.

People who want to "make a rule that covers everything" are basically pretty neurotic. For another example of this, see *religious fundamentalist*.

----

This is all very interesting, but don't we need getters and setters in Cocoa so that KVC works predictably?

----

Not really. If you have no get/set method, KVC just twiddles with the instance variable directly, and it handles the retain/release aspect of things as well. You only *need* get/set methods for KVC if you need to do something else besides set an ivar.

----

Why is CocoaDev turning into Slashdot? Offsite links and persuant discussions seem out-of-character with this site. � BrentGulanowski

*Oh, come on, GettersAndSettersAreEvil is an important (albeit an advanced) discussion. It just so happens that it refers to a previously written article. How many are there anyway? Two? (this one and ExtendsIsEvil). --Theo*

**Also, you will find off-site links on every single class page. See NSWindow, NSObject, NSResponder, etc.)**

