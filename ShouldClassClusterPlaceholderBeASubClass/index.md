---
layout: page
title: ShouldClassClusterPlaceholderBeASubClass
---



After reading the ClassClusters tutorial (great reading, thanks!), I still have a design question.

For clarity, I will call *public superclass* the public abstract superclass from which all the private subclasses are derived, and I will call *placeholder class* the class that is used in the     +(id)alloc method. This placeholder class is needed to create a temporary light object, and when the     -(id)init method is called, the final object is allocated as one of the *private concrete subclass*.

In the ClassClusters tutorial, the *public superclass* does not have any instance variables, and the *placeholder class* is a subclass of the *public superclass*. When alloc-ed, the *public superclass* actually alloc an instance of *placeholder class*. The *placeholder class* does not have additional instance variables, so it is very very similar to the *public superclass*.

Thus I have two questions:


*I don't see why the placeholder has to exist in the first place. The *public superclass* and the *placeholder class* are basically the same. At least in the scope of the tutorial, it seems all the methods of the placeholder could be in the superclass.

*This leads to the 2nd question. What if what want some instance variables common to all the subclasses? I can't declare them in the *public superclass*, because the *placeholder class* would have them too (as a subclass) and would have to allocate the instance variables and then deallocate them when the final instance is alloced following the     init method (you want the placeholder to be light). I see two possible solutions here:

*create an *abstract subclass* that will declare all the common instance variables and would be a superclass for all the private concrete subclasses. Without the concrete subclasses even defined, I already have 3 classes! Plus, if somebody else want to subclass the public abstract superclass, it won't have access to these instance variables that are supposed to be shared by all the subclasses. And even if those instance variables are private, it is a big problem not to have them around. They might be used internally for some public methods.
*the other solution I see lead to yet another question: why does the *placeholder class* have to be a subclass of the *public superclass*? If the *placeholder class* is completely separate, then the *public superclass* can have some instance variables. When calling     alloc on the *public superclass*, you return a placeholder instance if the calling class is the *public superclass* itself, or you do the real alloc if the calling class is a private concrete subclass. This way, the instance variables are inherited by all the private subclasses, but you don't allocate them until the     init method is called.




What do you think? --CharlesParnot

----

That is an interesting question.  I think that the main reason why the placeholder is a subclass of the public class is so that the API methods can be overridden to give more useful information about how the selector is not recognized since the cluster is not being used correctly.  It is a good point, though, and I can't think of why this needs to be a subclass, off the top of my head.  Unless you want to have it respond in some special way, it does seem as though making the placeholder a subclass of NSObject would probably be sufficient.  Note that this isn't evident in the tutorial you mentioned since the public superclass did this for them.  This is not the general case since some of the public methods may be worth implementing in the superclass.

However, there is no real problem with subclassing the public superclass since allocating the instance variables takes no time (a larger chunk of memory is malloc'ed - which may take more or less time depending on the implementation and if a new page has to be requested) so long as they are not initialized.  If there is common code, shared by the subclasses, which requires these instance variables, then there is no problem with declaring the instance variables in the public superclass.  If only some of the subclasses share this code, however, it should probably be some intermediate class which declares them and implements the code, not the superclass (this is one of the concerns with allowing third-party extensions of a cluster).

The only other thing worth mentioning is that having lots of classes is not a problem since it helps with optimal code sharing which leads to smaller code size and easier modification.

Is that input of any use?

--JeffDisher

----

Yes!

I actually thought one really wants to avoid allocating all the instance variables that are going to be released before ever being used. Of course, if you don't init, at least you are not going to also alloc potential objects that make those instance variables, and this is your point. So I guess if you don't have too many instance variables (eg no double[65536]...), you are OK.

BTW, I followed your link http://www.livejournal.com/community/ood/ and found that interesting sentence: *This is useful because the allocation class method returns an instance of a temporary class which knows how to do nothing except for receive these initialization methods which, in turn, cause it to return yet a different object (traditionally, another sub-class of the parent)*. I found the comment in the parenthesis interesting: I realized that the returned object does not even have to belong to a subclass! I don't know why you would not want that, but knowing it broadens one's way of thinking its program design...

--CharlesParnot

----

I have, sucessfully, implemented a class-cluster without the intermediate class. The *public superclass* took care of selecting a concrete subclass, making my implementation more like the AbstractFactoryDesignPattern. 

--TheoHultberg

----

Thanks for the input, TheoHultberg... I am glad my question was relevant and that somebody actually did not use a placeholder class! Did you have any instance variable in your superclass, and what do you think of the allocation problem? --CharlesParnot

----

The public superclass did have an instance variable, which wasn't alloc'ed (as in     +alloc) until the concrete subclass was init'ed, and I see no problem in this, just don't release it in the public superclass (since that is not where it is alloc'ed). My cluster design was a bit of a quick and dirty hack, but it worked out quite well: a public superclass decides which concrete subclass to use depending on the type of URL passed as an argument to it's init-method. 

I'm not too worried about allocating a little larger chunk of memory by having instance variables declared in the public superclass, as long as it's just pointers it's nothing.

--TheoHultberg

----

I may have misunderstood the question, but it seems to me that the reason for a placeholder class is to separate responsibility. The public superclass is meant to be abstract. Since there isn't any way to declare a class as abstract in Objective-C, the responsibility of NOT allocating and initializing that class is left to the developer (this is why that class would not be made public in a framework). The placeholder class should contain all the logic necessary to instantiate and substitute a concrete subclasses of the public superclass for the placeholder class on initialization (this class would be made public). That is its ONLY responsibility, which is really good from a design standpoint.

Is it necessary to partition responsibility in this way for a class cluster? No, but chances are good that it will improve the maintainability and portability of your code.

If I'm way off, please respond and set me straight. This is just my .02

-- EliotSimcoe

The same logic applies to including instance variables in the public superclass. One would need to see an actual instance to say "don't put instance variables in the superclass", but I would tend that way unless there was a good reason. Again, .02 :) -- KritTer

----

It's definitely a good idea to separate responsibilities by having an intermediate placeholder class, just for good design. It's not much work, but it simplifies things. --TheoHultberg

