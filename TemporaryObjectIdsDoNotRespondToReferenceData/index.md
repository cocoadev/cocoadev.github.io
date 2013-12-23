---
layout: page
title: TemporaryObjectIdsDoNotRespondToReferenceData
---

When working on an application using multiple Managed Object Contexts, you might come across this error message when saving:

    
Temporary object General/IDs do not respond to -_referenceData64
*** Uncaught exception: <General/NSInternalInconsistencyException> Temporary object General/IDs do not respond to -_referenceData64


I believe this is caused by mixing the Managed Object Contexts used within a Managed Object.

e.g.

Object A has an instance of Object B.
Object A belongs to MOC Z
Object B belongs to MOC Y

When you save MOC Z, the above exception will be thrown. This is because you're trying to save objects in one context, when they belong to another.

**
Solution:
**

Make sure that ALL of the objects and relationships in your code are instantiated with the SAME correct Managed Object Context (particularly if you're using some convenience methods in a singleton class to make grabbing the MOC easy). Easiest way to do this is     [self managedObjectContext]. Common points where this might occur (where default objects may be instantiated):

    - (void)awakeFromFetch;

    - (void)awakeFromInsert;

-- General/JeremyHiggs
