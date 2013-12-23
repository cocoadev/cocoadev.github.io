---
layout: page
title: KindOfClassSmell
---



If you find yourself using     isKindOfClass, stop! First, think whether you would be better off using categories and polymorphism. Second, consider using     respondsToSelector or even     conformsToProtocol.

Take the following:

    
if ( [temp isKindOfClass:[MyFirstClass class]] ) {
    // Do something
} else if ( [temp isKindOfClass:[MyOtherClass class]] ) {
    // Do something else
} 


Would you be better off introducing a new method,     doSomething, to MyFirstClass and MyOtherClass?

    
[temp doSomething];


This is much clearer, and easier to extend if you want to add another class to your system. If you don't own the classes in question, you can still extend them with ClassCategories. You will also find it much easier to reuse this code elsewhere! (See also: SwitchStatementsSmell.)

Alternatively, you might write this:

    
if ( [temp isKindOfClass:[NSArray class]] ) {
  enumerator = [temp objectEnumerator];
  // Do something with enumerator
}


Would you be better just checking for     objectEnumerator?

    
if ( [temp respondsToSelector:@selector(objectEnumerator)] )  {
  enumerator = [temp objectEnumerator];
  // Do something with enumerator
}


This makes the constraints clearer, and lets us substitute other appropriate classes later if we want. (See also: DuckTyping.)

----

*Also note that this "checking for objectEnumerator" completely breaks if you decide to listen to the stuff on the ObjectAsCollection page.  In uses such as the one on MutableDeepCopyAndUserDefaults you'd get infinite recursions.*

The KindOfClassSmell can often be avoided using the VisitorPattern, although visitor is not as elegant in ObjC as it could be. --Theo

