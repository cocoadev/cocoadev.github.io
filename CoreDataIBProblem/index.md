---
layout: page
title: CoreDataIBProblem
---

I've run into a problem, looked for documentation, and then pared down the problem, and looked for documentation. 
as far as I can tell the documentation suggests that what I want to do is both possible and easy.  At no point does any documentation explain it, and at no point is it self evident.

what I have:
an xcode 2.1 document based Core data app
the document model:
1.entity (identity) with 1.attribute as a string (name).
my document nib file  has  1. text view

and NO code at all.


I am able to easily use a relationship of my  entity (identity) to control the content of the text view (using "selection" in the controller key box),  this is what all of the documentation hordes over.
BUT 
I want to control the content of the text view WITH my single attribute of my single Entity.  
Apple says:
"There is no need to write custom accessor methods for properties that are defined in the entity of a managed object�s corresponding managed object model."
but there is also NO documentation on how to refer to the attribute of my entity if I am NOT using an array controller!!!!
it Should be easy, straight forward, plain, and yet... it is not.  So far it is an unmitigated failure, exceedingly more difficult than populating an outline view.

It all comes down to the Bindings panel in IB.  I go to the value tab, and try to hook up my working entity, to my working text box.
there are three options (2 are combo boxes, one is a pulldown menu) that are of major importance...
bind to
controller key
and model path.

from my understanding, bind to should get hooked up to my entity, controller key should either be blank or it should be my attribute name.
well, blank is NOT an option.  and using my attribute name throws an exception... "...was sent to an object that is not KVC-compliant for the "name" property."  Apple has already told me that this IS Not the case. (see above)  and the last option IS optional and so far it has not been of any help whatsoever.


what exactly should I be putting into the Bindings panel, value tab to make my text view Bind to my entity's attribute?


*How about the attribute's name? :-) It's just that easy. Your array controller should be set to use an entity (specify its name). Bind the text view to the array controller's selection, using the attribute (name) as the key path. Your array controller's managedObjectContext binding should also be bound to an appropriate context. If it's document-based, just point it at File's Owner - it'll automagically pick up managedObjectContext in the key list.*

