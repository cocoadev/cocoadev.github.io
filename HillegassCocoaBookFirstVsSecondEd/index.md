---
layout: page
title: HillegassCocoaBookFirstVsSecondEd
---



I have gone thru the first edition of Aaron Hillegass' "Cocoa Programming for Mac OS X."  It has been a help - but I think I slightly prefer some of the more recent O'Reilly books.  The new Second Edition is out and it's XCode-centric.  Does it have compelling content changes to prompt a purchase of it, as I've got the first?  

Thanks.


blakespot

----

"Don't just give a man a fish; teach him HOW TO FISH."

Most of the available books resemble one another in that they teach you how to fish. More or less.

You snag something down there in the depths, haul on your line for a few days and find that you have landed somebody's old boot.
It gives you the feel of fishing. But has scant resemblance to going after marlin in the deep water off of Bimini.

If you've read one of these, you've read them all (except maybe one).
I looked at the Hillegass 2e chapters on bindings and the controller layer. It's the Chinese meal of Cocoa pedagogy. An hour later you're confused again.

----

Thanks...  Though your post is somewhat confusing, I must say.

> If it wasn't confusing at the beginning, it wouldn't be Cocoa, would it?

blakespot

----

Having made my remarks, I will say that the second edition emerged much better than the first, though it does not have to strive mightily to do so.
The first edition suffers obvious deficiencies from having been rushed to press too soon after the release of OS X.

The second edition is much cleaner. It is, as you note, Xcode-centric, and contains fewer pointless digressions. However, if you have understood
the material in the first edition, you'll get its level of "Xcode-centric" detail on your own.

What I can't understand is why most of these authors, who obviously possess deep knowledge of the frameworks and the object-oriented
paradigms that they incorporate, end up failing to summarize clearly their dozen or so organizing principles. Hillegass, given his additional years of NextStep experience is doubly guilty of this. If he succeeds relative to his colleagues, it is because the rest are so bad.

I think BookCocoaRecipes is an example that wasn't "rushed". But it goes for everything-and-the-kitchen-sink in the API, and totally obfuscates the organizing details. Somewhat obsessive-compulsive in its approach.

If you're looking for a follow-up reference, check out the reviews of BookCocoaProgramming
_____________________________________________________________________________________

I had some previous programming experience but no knowledge of Cocoa or Objective-C. I thought the first edition of Hillegass was absolutely outstanding.  If you really understood (almost) everything in the first edition, you don't need the second edition.  Otherwise, I think you would find it very worthwhile.

----

You also have to understand the pedigree of the book.  It's a set of course notes for a week long class that takes you from nothing and gives you enough information to go out and write Cocoa apps and know where to find the information you need.  Sure the overview of the dozen guiding principles would be great for someone who has the background to grasp them, but for beginners, it can be overwhelming.  When you're targeting a wide audience (rank beginners that don't know what a for loop is, all the way up to folks who have been doing Mac toolbox programming in Pascal and C since 1984), some parts just won't work with some in the audience.

----

Here's an example of what I mean:

On page 134 of the first edition, Hillegass spends a page discussing     - (void) encodeConditionalObject: (id) object, a topic
essentially useless to the beginner, who could much better benefit from an overview of how Cocoa's object-oriented idioms
all tend to reflect the same underlying approach.

Given that pp.61 - 64 contain a mainly-pointless introduction to NSCalendarDate, Hillegass has already wasted five or six pages
that ultimately deprive the student of some key expertise.
This isn't mere catering to the LCD:  The book contains other examples of this kind of wool-gathering.
Did the book's reviewers suggest he try harder? No one will ever know. Did anyone even review it?

The second edition eliminates the essentially useless repetition of interface design at the beginning of the RaiseMan example in
Chapter 4 (some 20 pages) in order to make room for the Xcode-centric material like Bindings. But you get the idea.

My comments about old boots and marlins stand. Why give the reader something he can REALLY use?
Even when you have the experience to illustrate it. Maybe the guy who just buys the book will STILL need a week at BNR.

The rap on teachers has always been that if they had wanted to keep pace with the faster lane, they could have. Or couldn't have.

Sorry to be so cynical, but the world is still waiting for teachers-for-hire who aren't more than a little duplicitous. Or simply lazy.

----

Hillegass isn't lazy, but he tends to focus in the wrong areas. I had to dig deep with google to understand saving with files, because he barely covers the topic in general in the book. I wish he would have covered the fileWrappers and such more.

----

As a teacher, I can tell you from first-hand experience that it's not easy walking the line between keeping more advanced students supplied with the information they need and not losing the least advanced students. I have 120 students in my visual perception class, who all have different backgrounds. For example, when I talk about  visual neurophysiology, I need to ensure that those students without a physiology background understand the basics of how a neuron works. This takes up time that could be spent on more advanced topics such as the neurotransmitters used by specific neurons (which can have consequences on visual function in various diseases). Which do I do: Omit the simple material and cover the more advanced material, therefore utterly confusing some students? Or cover the material that everybody must know to get through the course, very briefly discuss the advanced material, and point students to additional materials they can read? 

Aaron faces the same problem that all teacher do. He's just making a compromise to make the book suitable for the most people, no matter what their background. Suggesting that he's duplicitous or lazy is harsh and unfair.

----
   I would like to express strong agreement with the comments just above.  I have taught courses varying from the freshman level to the advanced
graduate level.  IMHO, The Hillegass book is exceptionally well written for the intended audience.  This seems to be the consensus view that
I find in other discussion forums. 

----
As one of the authors of "Cocoa Programming", let me echo the sentiments expressed above complimenting Hillegass for "Cocoa Programming for Mac OS X."  It is very hard to write for a broad audience.  "Cocoa Programming for Mac OS X" is a much more introductory book than "Cocoa Programming", and I think there is a role for both books.

Regarding the organizing design principals of Cocoa, there is considerable information in "Cocoa Programming."  There is a brief chapter on design patterns.  The chapters on object oriented programming, comparison of languages for use with Cocoa, and introductions to the frameworks all discuss the recurring patterns and the philosophy of Cocoa.

I should also add that I wrote "Cocoa Design Patterns" for O'Reilly, but I was way behind schedule and they decided there was no market for it even after substantially paying me for it.

- Erik M. Buck

----

What happens with the stuff you've already written?  Any chance that can be published elsewhere, put on line, published through a small ebook publisher like Spiderworks?

----

blakespot,

     Can you give some examples of more recent O'Reilly books that you like?  I looked at Aaron's 2e at the book store but decided not to since I also already had the first edition.  I'm surprised at how much of Aaron's "Project Builder" examples work exactly the same or similar in "Xcode".
     I also hace "Building Cocoa Applications, a Step-By-Step Guide".  Lots of redundancy between the two.  Neither goes into file I/O in enought detail.

----
 
What I like to see about those books ( all of them ), is a part two approach. The second edition ( read 'part two' ) should be a book with new, updated and deeper things . The first didn't have bindings and CoreData? The second should. The first did have basic about memory principles, the second deeper. People will buy both of them and people who did buy the first will have more satifaction when they buy the second.

----

The problem with that approach is availability of the first edition.  When the second edition of a book comes out, the first has either sold out or gone out of print.  It's not possible for someone to buy the first to go along with the second.  At least in the case of the Hillegass book, it covers some of the same territory as the first, but that territory is radically changed due to changes in Xcode and due to bindings (for fun, compare the two versions of Raise Man).  the second edition also has other goodies like applescript and an intro to OpenGL.  That's what makes it worthwhile to get the second edition.

----

The economics of publishing (and of development frameworks) mean that books about the frameworks become not-quite-current quite rapidly.
That the first edition of Hillegass (or Building Cocoa Applications or Vermont Recipes for that matter) are still very useful references for the
beginner is a testament to how solidly the frameworks are designed. The economics of publishing dictate that there will not BE a useful
little pamphlet on CoreData (or on Bindings before it) sold at newstands for 4.95. There will never BE a book on socket programming for
dummmies, because it is not a beginners' topic. And the non-beginners do not need a book enough to pay off the publisher.

Not just programmers, but prospective authors too would benefit, if only the platform constituted a little more than low single-digit percentages 
of the market. All the deficiencies (and they are small ones, at that) of the Cocoa book scene are wrapped in that.

----

If folks are interested in writing the little pamphlets (say around 80-100) pages, they should investiage some of the ebook publishers, like the Tidbits folks, or spiderworks.

----
Reverted spam.
----
 Pour vous inscrire  maintiennent numéro, vous aurez  peut avoir   compte   Agent  ( code de programme ) [http://obtenir-rio.info rio bouygues]. Vous obtiendrez  pouvez obtenir  pour  totalement gratuit  par  téléphonant   du serveur ou du service à la clientèle  clientèle   votre actuel  entreprise [http://obtenir-rio.info/rio-bouygues code rio bouygues] . Vous ne  certainement  get un SMS  utilisant votre. Avec  votre propre  [http://obtenir-rio.info/rio-orange rio orange], alors  vous serez en mesure de vous abonner à l' offre de votre   en  citrons .

