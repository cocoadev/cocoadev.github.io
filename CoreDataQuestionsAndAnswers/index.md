---
layout: page
title: CoreDataQuestionsAndAnswers
---

**Question:
Whose idea  was it try to eliminate the controller layer?
** 

**Answer:** 
This goes back to early Smalltalk work.

**Question: Is there a history to trying to do this outside of Apple/NeXT?** 

**Answer:** 
Microsoft tried with DDE which I think stands for "Dynamic Data 
Exchange" and the DoExchnage() features of MFC classes.  There is some 
support for automating these controller tasks in MS Visual Studio's 
various editors.  Visual Basic from Version 3 at least had bindings capable
controls using the COM and DAO (later ADO) subsystems to hook database
data directly to onscreen controls.

 Borland's Delphi, IBMs Smalltalk, Java, and C++ 
environments provide some automation. 

Ref: Microsoft's "Managed 
Objects"  .Net and C# seem to emphasize bindings like capabilities, but 
I admit to having only casually explored .Net.

Some here might even argue that "signals & slots" from the Qt framework automates some of 
this in the same way "Targets and Actions" automates some of it. e.g. 
Automated menu validation and simple connection of menu items to 
objects eliminates a lot of controller code needed in other 
environments.

**Question: If so, do those approaches go by a different name?
Do other platforms that don't have the benefit of key/value runtime
introspection have a different solution to this kind of thing, or do
they all use some kind of "yucky controller layer" ?** 

**Answer:** 
Yes, yes, and yes.

**Question: Is this database technology?** 

**Answer:** 
I seems similar to "Enterprise Objects Framework" (EOF) which Apple 
still sells.  EOF has more of a database centric view of the world than 
CoreData.

**Question: Is it "normal" to apply database technology to application design?** 

**Answer:** 
My guess is that 90+ percent of all software written is database and 
business automation related.  In that context, I would say not using 
database technology influenced design is abnormal ;)

**Question: Is there a debate about whether database "entity modeling" should be
used in desktop applications? ** 

**Answer:** 
Yes.  I think Apple engineers would be the first to tell you that 
CoreData is not the best solution to every model layer problem just 
like the MVC pattern is not best applied to all application designs.  
However, in both cases, the techniques are applicable to a surprisingly 
broad range of applications.

**Question: Should I feel warm and fuzzy about designing my application's model
this way instead of in a more "dictionary-oriented" way? ** 

**Answer:** 
No.  The view of objects as dictionaries is just one implementation 
technique, and it is useful but not very often the best solution IMHO 
:)  I would say a better contrast would be entity relation diagrams and 
traditional OO decomposition.

*Could you elaborate on this, please? I'm very interested.*

**Question: Are there times that I should favor "traditional" model design over
this new format? ** 

**Answer:** 
Yes.  If the application is primarily computational instead of data 
management oriented, I would probably not choose CoreData.  I doubt 
Core Data can substantially automate or improve a ray tracer, a fractal 
generator, a PDF viewer, an industrial equipment controller, a low 
level DVD burner, many games, etc.  In fact, I am starting to think 
that "real time" and Core Data are mutually exclusive.

I can't wait to see Apple's Sketch example re-implemented with 
CoreData.  It could be Cool!

**Question: Is good database design equivalent to good application model design?** 

**Answer:** 
No.

Apple has added a powerful tool to our tool boxes.  That is all.  The 
tool is not particularly original (To the CS field or even to Apple) in 
concept, but it probably is in implementation.

