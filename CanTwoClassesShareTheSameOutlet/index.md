---
layout: page
title: CanTwoClassesShareTheSameOutlet
---



Is it possible to have two instantiated classes share the same outlet (I'm not talking about 1 button having two actions, rather two different classes each having a reference to the same outlet)? I've tried to set this up in IB (a NSButton in my case), but one of the classes just sees it as null. Is there a special condition that has to be met (the class be file's owner, delegate?) for this to work?

**No, outlets just work. There is no special requirement as to what the object must be, and there is no way for two outlets to the same object to somehow interfere. You're doing something wrong and it's not related to having two outlets.**

*Shortly after I posted the question I thought about it some more and figure out why it's not possible (at least the way I did it in IB) for it to work given my setup; for the class that it does work, that class is the "file's owner", so it owns the nib and the outlets get connected at load/launch time. The other class, though instantiated in IB, doesn't have an instance at the program launch, in fact an instance of the class is only created later on, if ever. Since that class doesn't yet have an instance, there's nothing to connect the outlets to. The result is a null reference when trying to access the outlet(s) from this second class (once it does get instantiated)*

----

This question is rather difficult to parse, particularly with the OP's elaboration of it. An outlet is only an instance variable that you declare in a class to point to some object that is then instantiated in your nib file. If you want two classes to point to the same nib object, by all means be my guest. You may get headaches from doing it, and sometimes it even becomes necessary. Nothing in the behavior of nib file loading prevents it. This wiki has several good pages on what outlets are and do.

The original question (and subsequent attempt at explanation) suggests a deep confusion, not only about what an outlet is, but even about what classes and objects are. This has contaminatied the discussion here far more than any suggestion to delete the page. Trying to guess what a confused newcomer is asking (perhaps also having to express it in a language that is not his first) is a recipe for acrimony.

----

If there is an object in IB, then it *is* instantiated. He did not solve his problem, he just found some kind of thing which somehow made sense to him. If he's content with his "solution" then that's fine, but this page contributes nothing useful to the site, since it's just a question, a very basic explanation, and finally a very, very wrong conclusion. The OP's attempt at explanation makes no sense whatsoever. 

**Then explain why it doesn't connect to the second class if the "outlets just work" as you claim (and yes there is now sufficient information to determine cause/solution, and I bet you didn't even try it the way it was explained before you commented again)**

Possible reasons:


*The class of the object in IB does not exist when the nib is loaded, or simply does not exist at all.
*The class of the object in IB does not actually have the indicated outlet.
*The class of the object in IB has a setOutletName: method which does not actually set the given outlet.
*Something else I haven't thought of.


There certainly is not enough information to decide between these possible scenarios, not by a long shot. Every time you connect a window's delegate or a table view's dataSource, you're connecting the outlet of an object that's not File's Owner, and people connect multiple outlets to the same object *all the time*. It's all very kind of you to assume that the original poster has stumbled on something fundamental here, and that the responders have no clue, but it's just not the case.

I already explained the error, as I'm the one who wrote the original, "outlets just work" paragraph. There really is nothing more to say than that, because it's absolutely true. This really is one of those things where you can't say much beyond "no, that's completely wrong, try again", because we simply don't have enough information to say more

 "Two classes, one outlet" is something which simply makes no sense. I interpreted this as meaning that two objects had outlets which were connected to the same object. It is possible that I am wrong. 

----
Yes, I speak english very well, and yes I *do* know what an outlet and class is (you do know what assumptions cause, right?). No, I don't know why people take a simple question and turn it into some complicated notion. And if someone reads "2 outlets 1 class" as "2 classes 1 outlet", well I can hardly be held responsible for that, especially since I even clarified it to precisely avoid such confusion.

*Above, you said, "two different classes each having a reference to the same outlet". How can that possibly mean, "2 outlets 1 class"?*

*Here is one of my greatest annoyances about bad questions. Somebody comes, asks a question, gives an absolute minimum of information, and then after all of that, he has the **gall** to complain when the people who are trying to **help** him make "assumptions". What **else** are we supposed to do? You don't give us enough information! Our choice is either to make assumptions or ask a ton of teeny little questions with the subsequent roundtrip time involved. And as things typically go, asking those teeny bits of information usually results in complaints as well.*

*If you are interested in an **answer**, rather than simply heckling people who dedicate their time to this site, it would be good to make some attempt to clarify the situation instead of just complaining about the quality of the help*

*I am tempted to argue that question pages such as this should be off-topic for this wiki. The wiki format is fairly badly suited to carry on question-and-answer discussions in any case, and the anonymity, fast editing, and confusion that occurs when people don't take the utmost care to organize the page in the middle of the discussion, all tend to cause a great deal of pain. We do great with theoretical discussions or informative pages, but our track record is not so great when it comes to this kind of page. I'm not trying to say that wikis are bad in general; in fact, I think they're the greatest thing since sliced bread, but they are perhaps not the appropriate method for solving newbie problems.*

I disagree whole-heartedly. A Q&A page can easily turn into a valuable resource. In fact, most of the pages on this site that I found useful as a beginner were Q&A pages (that weren't even refactored). If it's a Q&A that actually has an 'A', then remove the discussion of the 'Q', refine the 'Q', and make the 'A' more of a 'documented fact'. This is called 'refactoring'. This is one of the primary ways a wiki grows. Additionally, after a few years, I've become a pretty darned knowledgeable person myself and have contributed a great deal over the last year and a half to this wiki (actual how-to pages that started out as my own or someone else's Q&A page). - Anonymous1234

Yes, I agree, it's just painful experiences like this one which make me doubt. We must realize that the Q&A process is a two-way street. Yes, this means that the people writing the answers must keep an open mind and try to do a good job, but it *also* means that the people writing the *questions* must try to take care of their part of the bargain. I really hate it when somebody's question is obviously badly-worded and causing problems, but as soon as someone actually says so, the person who asked it usually gets very upset and accusatory. Above all, people who post questions need to understand that this is a site visited and run by volunteers, and that getting an answer to your question is something which happens because someone who knows the answer decided to use some of his free time to answer it, and that getting an answer is not any kind of right. -- PrimeOperator

Too lazy to search right now, but I thought there was a page about how to ask questions ... maybe HowToAskQuestions ... ? ;-) At any rate, if someone posts a 'dumb question', reply with a link to that page. Keep the smart-a$$ tone out of the message, too, and nobody could argue you didn't do the right thing. If the question's a bad one, this doesn't say "your question's a bad one, dumb-a$$", it says "you need to rephrase your question - here's how". - Anonymous1234

(Further discussion moved to HowToAskQuestions.)

----

SignArguments. -- KritTer
----

*Above, you said, "two different classes each having a reference to the same outlet" How can that possibly mean, "2 outlets 1 class"?* It doesn't. **You** mixed it up again. My original question never, ever implied 2 outlets. That was even clarified; which matches the title CanTwoClassesShareTheSameOutlet (ie. Two (2) classes sharing the same (1) outlet) -OP

OTOH, you later posted "...if someone reads "2 outlets 1 class" as "2 classes 1 outlet"...". Perhaps you wrote that backwards? I think this was the point of contention -- KritTer

*That was when I was pointing out that the person complaining about the question had mis-read my original post - that wan't even part of intended original discussion at that point* -OP

I think it pushed Anonymous1234 over the edge, though. (Poor chap has a short tether ;) -- KritTer

This page has proven useful for generating a meta discussion, but that has been moved to another page now, and this page contains nothing of note. If the original poster is still interested in an answer or discussion about his problem, I would propose that he delete the current contents of the page and re-post a question, hopefully in a clearer form. If not, then perhaps it is time to get out the DeleteMe, since we're not going anywhere otherwise. -- PrimeOperator

----
A pointer to HowToWriteHelpfulAnswers seems also relevant.

