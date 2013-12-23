---
layout: page
title: SingletonAlternatives
---

IMO, clean and simple communication between objects is one of the most difficult tasks in any OOP language (next to coming up with names for classes and variables). It quickly becomes overly complex when more objects are added to the project. I originally thought the SingletonDesignPattern was an easy way out. It provided convenient access to global data and services which any object could use. Although singletons are the best solution in **some** cases, **most** of the time there is a better alternative.

There are two reasons why one might use a singleton: to access global services or to access global data. In order to find the alternatives to the singleton, we must determine what the singleton is used for in each situation. If it is a combination of the two (a global service acting upon global data) then I would consider the reason is to access global data.


**Global Service Alternatives**

If a singleton is simply acting as a service - that is, no instance variables - here are a few alternatives:


* Place the service/method inside the object whos data is accessed most frequently in that service/method. Normally this is the best alternative but may require adding a category if you can't change the class.
* Create an instance of the class every time you need the service. If the latter approach slows down the application too much, then go with a singleton or another alternative.
* Use all class methods in your singleton. This is basically the same as a singleton but it provides quicker access to the service (you don't have to get the instance of the singleton).
* Use C functions. If you need to access a service frequently (in a tight loop for example), this may be a better approach for optimization purposes. Other then that, I think it's more of a personal preference.



**Global Data Alternatives**

In most cases, a singleton's main purpose is to store global data and provide access to it. The alternative to this design is difficult to pin-point because it calls for a different way of thinking about OOP design. Therefore, you may not be able to easily replace a singleton with an alternative because the rest of the design will not fit well. Instead, before using a singleton to store global data, consider this alternative.

Instead of retrieving the global data from a singleton, pass it as a parameter. This is prefered over using a singleton because it improves encapsulation.

To go along with the previous point, if you are passing along the data as a parameter so the receiving object can act upon it, consider using the data object to handle the action instead. This way you do not need to pass it anywhere. In short, if an object is messing around with another object's data, ask the object itself to mess around with its own data (part of the LawOfDemeter).

If you still find the same object constantly being passed around everywhere, you may consider using a singleton or else changing the core design of the project.

I hope this helps some in their design decisions. I'm still learning (aren't we all?), so please feel free to comment/correct me on this topic. -- RyanBates

----

It's always good to have options and alternatives, but I get the feeling that the text above recommends not using the SingletonDesignPattern, even if that is not the case (and I guess that it isn't), it should be mentioned that it's always better to follow a design pattern, and if the alternative is not a design pattern, there should be a very good reason to why to switch.

Singletons are (as RyanBates mentions) a clean way of doing for example global data retrieval and global services, it's easy to understand and can simplify things a great deal. If, however, you use singletons where you shouldn't (and that's not always easy to know, but look at the SingletonDesignPattern page to get some hints), look at the text above and refactor accordingly.

-- TheoHultberg/Iconara

----

While I do agree Singletons are not always bad, if the only intention for creating a singleton is to make an object-oriented way to access global variables, then I would consider looking for an alternative. There's more discussion about this at http://c2.com/cgi/wiki?SingletonGlobalProblems and http://c2.com/cgi/wiki?GlobalVariablesAreBad

-- RyanBates

----

Alternative: use class methods and variables (MUCH easier in Java, then you don't have external declarations in the implementation file and the like...) This is basically a compiler-enforced singleton, but allows you to ignore ALL the usual singleton code. Then just make a private constructer/alloc to keep others from creating an instrance. See java.util.Arrays --JediKnil

*The problem with this is that, when you change your mind and decide it should allow multiple instances after all, you're basically totally screwed. This is why I stick with singletons as instances of classes.*

