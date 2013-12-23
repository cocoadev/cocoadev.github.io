---
layout: page
title: UserInput
---



I have developed an Objective-C code which does some processing on images. I have a UI for the code. Currently several parameters are hard coded into the code. I would like the parameters to be user customizable. What is the most elegant way to approach this?

I have thought of two ways:

1) Declare all the input parameters as global variables (the variables are int, float and BOOL. May be strings and char in the future). However, there is always a danger in having so many global variables. So a better approach would be to use a struct with all these variables as members and make the struct a global variable. This way, I just need to worry about a unique variable name for the struct alone. 

2) An alternate way would be to keep the struct local and pass the struct variable to all functions and sub-functions. 


I just feel that there are more elegant ways than the ones I mentioned above. Is there something in the Xcode library? Or does C itself have some inbuilt functions for that?

Thanks,
Nevine

----

How about General/NSValue objects?

----
Or, you know... objects?

----

Global variables are not needed. Ever. So start to think about never using a global variable ever again. The correct solution to the problem that global variables seems to solve in some cases are class variables. If you need to refer to a class variable from another class, write an accessor for it. If you find yourself needing to refer to other classes variables a lot, and as "hidden parameters" to functions and methods, you might want to consider the possibility that your design is wrong.

----
The correct solution here is to use the General/ObjC object system to do the things it was designed to do and create a class which encapsulates all of these settings, then instances of this class can be passed to anything that needs those settings.

----

Thanks for the suggestions. I am not a hardcore programmer, so I do have some basic questions. I apologize for that.

From your suggestion, I believe it's along my second method, wherein I keep that struct local and pass it to every function and sub-function which needs it (and according to you, use a class object instead of a struct instance). Is that right? And why is it better to use a class instead of a struct? All I need is encapsulation of data (no methods) and a struct provides that. Also a struct might have lesser overhead as compared to a class. Am I wrong? 

Also what about the suggestion on General/NSValue? You want me to use that rather than using my custom class? From my limited perspective, I see that this class object needs to be sent to every function and subfunction which needs it. If however it was global (by naming it something like __userParameters__, to avoid conflict), I could avoid changing the argument list of all the functions. 

I also have a question on alloc and release but I'll ask about it once this doubt is cleared. Look forward to hearing from the gurus.

Thanks.

----
You should use an object instead of a struct because you can.

Objects are more flexible, easier to manage memory for, and easier to change in the future. In theory you can use structs for everything and forget about this whole Objective-C business, but we don't. If you ever want to change your parameter object so that it performs lazy loading or computed values or something else of that nature, the change is trivial. As for the question of overhead, you are correct that a struct has less overhead, but who cares? Computers are fast, this will not be a bottleneck, and General/OptimizeLater.

As for altering everybody's parameter lists so that they can refer to the parameter object, this is why we have General/InstanceVariable<nowiki/>s.

----
Thanks again. Yes, it does make more sense to use a class instead of a struct. There would be a need to set default values in case the user does not provide all input parameters, in which case, I guess a class with appropriate methods would make things much easier.

And as far as user parameters is concerned, it's a one time setting. Once set, it does not need to be changed till the processing is completed. And the reason I mentioned about global variables is because there are several functions which depend on these user parameters and in order for these functions to have access to these parameters, I must pass the class object to them (unless if it is global). But again, it's just my amateur point of view but before I choose a direction I just want to make sure why the other directions must be avoided.


----

Globals are bad because they make a function non-portable, difficult to maintain and debug, and susceptible to bugs caused by other functions accessing the same globals. One of the key advantages of object-oriented programming is code reuse - you can take a class designed to solve a problem in one space and deploy it in another to solve the same or similar problem. As soon as you introduce globals you mess that up - the code becomes tied to its globals. By the same token if the code required those globals to function they are hidden parameters - you cannot read the code (or just the header) of the class and know how it works or how to use it without additional documentation about how the globals affect it. Finally, if another piece of code touches those globals between invocations of the class that uses them, you get different results, even though you've done nothing different. So that means you've created a dependency between two pieces of code that could otherwise be independent - thwarting the very point of object-orientation. My view is that globals are never needed at all. The right way to implement variables that are static with respect to multiple instances of a class are class variables (in Cocoa, those that have a '+' sign in front of their method names). This maintains portability of the class because those variables go with the class if you use it elsewhere. Because they are part of the class, you can see at a glance that they exist and may effect the way instances operate. While external code can still change them (if you are providing "setter" accessors), at least it's through a defined interface and with (self-)documented and defined results. --GC

----
General/NSUserDefaults....
