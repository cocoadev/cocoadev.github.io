---
layout: page
title: FoundationMostWanted
---

    
@interface NSEnumerator : NSObject

/********
**
** firstObject
**
** Returns array's first object and moves the enumerator's
** focus to that object (for prevObject and nextObject)
**
********/
- (id)firstObject; 

/********
**
** - (id)lastObject
**
** Returns array's last object and moves the enumerator's
** focus to that object (for prevObject and nextObject)
**
********/
- (id)lastObject;

/********
**
** - (id)prevObject
**
** Returns object in array that has index of (focusObject - 1).
** Moves enumerators focus to Object.
** If no Object, returns nil. Sets focus Object to nil.
** Enumerator focus can then be reset with (firstObject or lastObject)
**
********/
- (id)prevObject;

@end


----

Enumerators are used for a lot more than just iterating through arrays. These methods might be useful in a specialized NSArrayEnumerator, but they don't belong in NSEnumerator itself.

Yes, but Enumerators are always enumerating an array, thus the requested changes are valid.

*No they aren't.  Each object might be being generated on the fly when it's asked for.  There really might be no array involved.  Definitely firstObject has no meaning.*

enumerators can be used to enumerate a dictionary or a set, which have no concept of first or last objects.

----

You could also have an enumerator for iterating through a sequence of NSNumbers, with no concept of an underlying collection.

Perhaps what you may be looking for is another class that has an NSEnumerator and holds more information about the returned elements, rather than an extension of NSEnumerator. That way you could make any collection do this sort of stuff. Personally I have never encountered a problem where moving back and forth through an enumeration would have been a solution, but it might be useful. What is this intended for? -- MikeAmy

----

Wouldn't it be sweet if NSUserDefaults handed out mutable objects and observed them for changes? Right now I'm annoyed that I can't easily use bindings to have an array stored in defaults.

*Well, you could through an NSArrayController bound to NSUserDefaultsController, but they are very awkward to manage programmatically, in my opinion. -- JediKnil*

----

Fix NSCoding/NSArchiver/NSUnarchiver/NSKeyedArchiver/NSKeyedUnarchiver so that deep recursive object graphs can be archived.
 Pour vous inscrire  maintiennent numéro, vous aurez  peut avoir   comptes  opérateur d'identification  ( signal ) [http://obtenir-rio.info numero rio]. Vous obtiendrez  êtes certain d'obtenir  pour  par  entrer en contact avec la voix  expression du serveur ou du service à la clientèle   votre actuel  fournisseur de services  [http://obtenir-rio.info/rio-bouygues code rio bouygues] . Vous ne  recevez immédiatement  un SMS  avec vos . Avec  votre actuelle [http://obtenir-rio.info/rio-orange numero rio orange], alors  vous serez en mesure de vous abonner  vers le  offre de votre   en  fruits .

