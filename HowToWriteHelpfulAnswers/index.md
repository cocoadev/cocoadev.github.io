---
layout: page
title: HowToWriteHelpfulAnswers
---

see also HowToAskQuestions - How to ask a good question to get a good answer

Newbie questions about the basics and frequently-recurring questions (at all levels of sophistication) are part of life on a development wiki.

There is a good way to write helpful answers to questions we receive, and there is a bad way. Try your best to be friendly, clear and concise with your answer, and provide plenty of information on where to start, what to do if you get stuck, and where to go if plan "A" is successful, or if it fails. If a plan "B" is available, try to give some info on it. Your first impression of what the question is about may not be correct.

Gave your interlocutor the info he needs: what to do, what the choices are to get this info, rather than "Just Google for it" or "it's on developer.apple.com, so look for it!". This helps him if he wants to try for himself, and when he goes to other pages, he'll know exactly what to look for.

Though it may sound kind of sappy, a helpful answer will go a long way. Even if we never hear from the person again, it will give that warm fuzzy feeling inside to know that we've done what we can to help them. This info is useful when responding to questions here on CocoaDev, and helps to promote the Apple platform (and will lead to a better reputation for CocoaDev.

Also, if you don't feel like repeating information that is available elsewhere, at least give the user a direct link to said info, and make sure that said info is still there, so that they aren't searching for data that doesn't exist. A series of links is still helpful, as long as it can answer their question. If you aren't able to answer the question, if you've been asked directly, say so, and try to help them in another way, the best that you can. Simply saying "look for it yourself!" isn't all that helpful, and usually leads to rancorous exchanges (a polite name for flame wars).

----

On the other hand, if a question is clearly answered in the documentation, a simple link to the docs or just a pointer like, "see the NSString docs", should be plenty. A good example, which follows, illustrates this better than any theoretical discussion of the niceties of searching.

*The following was an actual exchange that took place here at CocoaDev*

I have just started delving into Obj-C programming, and I'm trying to understand the relationship of objects to classes.
I have created my own small class and can access the instance variables via my own accessor methods.

How do I access the values stored in my class instance from within other classes?
When I tried to do this, I was told my Class instance is undeclared.

Thank-you :)

----

You have to declare the instance in the other class in order to use it in the other class. Declaring variables before you use them is a basic requirement of C programming, and since Objective C is a superset of C, you are certainly bound by those syntactic rules. In order to declare a variable of the type represented by your class (or use that class's methods), you have to include the header of the class in question.

When you understand those basics, our page AdviceOnImportingHeaders may make a little more sense to you

----

Another thing you may be misunderstanding is that, in ObjectiveC, you create *objects*; a class is simply a template. When you write methods, the "self" object references AnObject you are actually manipulating, which will have its own copy of all the instance variables of the class; equally, if "myState" is an instance variable, when you refer to "myState" within a method, you are referring to the particular copy of "myState" belonging to the object. So when you want to access "myState" from a *different* class, you need first to determine which object's copy of myState you want, and then pass that object a message to ask it what value its myState holds (or to change it); this method is called an accessor (or a setter).

----

I recommend that you read up on Objective-C before continuing. This truly is beginner information and you'd do yourself and this community a great service by reading some of the GettingStarted or CocoaIntroduction topics before posting questions.

That said, you typically want to write 'accessor methods' (that's a good term to search for in the beginner tutorials). I'd recommend the following articles:

http://www.cocoadevcentral.com/articles/000082.php

http://www.cocoadevcentral.com/articles/000083.php  (page 6/8 of this particular tutorial covers your question very well)

----

Thank you all for your suggestions - back to the books, I guess!  I hope this information may prove useful to others starting out with ObjectiveC. We all start as beginners, right?

