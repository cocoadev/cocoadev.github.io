---
layout: page
title: ObjectOrientedDesign
---



ObjectOrientedDesign is program design that makes effective use of ObjectOrientedProgramming techniques to give a good balance between factors such as code reuse, modularity, et cetera.

If this whole object thing doesn't ring a bell, you might want to read out introduction into AnObject and what it's good for.

*
To view an excruciatingly-long introduction to object oriented programming, read http://www.developer.com/design/article.php/3304881 - The author requires 2000 words to communicate something that would be much more understandable in 200. All the concepts are there, but you have to read for half an hour to learn things you could grasp in five minutes if the author or his editors had exercised any sort of discipline.
*

ObjectOrientedDesign often makes for a program whose code is much easier to edit, and in the case of libraries and API's, much easier to learn. It can appeal to the programmer's common sense, and can make programming easier.

On the down side, ObjectOriented programs run slower than their procedural counterparts, because more code is being executed, period. But this is not always noticeable, and ObjectOriented programs written in, for instance, ObjC have the advantage of being able to contain procedural code as well.

Overall, the advantages of ObjectOrientedDesign and ObjectOrientedProgramming far outweigh the disadvantages, except in rare circumstances where speed is of the essence. Such cases can  often be fixed by wrapping very fast procedural, or even assembler, code within an OO shell, optimizing the code that needs to be fast without losing the benefits of ObjectOrientedDesign in the program as a whole.

ObjectDecomposition is the mechanism for solving problems in an object-oriented fashion. ObjectDecomposition breaks a problem into its components (or objects) and their relationships instead of breaking a problem into its data and the operations (structural decomposition).

----

I don't like this section:

*On the down side, ObjectOriented programs run slower than their procedural counterparts...*

This is true if your code is very procedural by nature. However, if the problem you are solving is something very adapted to OO (sending lots of messages everywhere), to the point that you can leverage the existing message-passing system, then using an OO language can make your application faster and less buggy, since the language's implementation of OO concepts is almost certainly faster and less buggy than the ad-hoc version you'd be forced to grow if you used a procedural language.

However, I'm not sure how to distill these thoughts into something that fits in the above text. Suggestions?

