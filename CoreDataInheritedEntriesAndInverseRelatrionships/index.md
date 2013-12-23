---
layout: page
title: CoreDataInheritedEntriesAndInverseRelatrionships
---

Hi,

I'm having a small problem with Core Data.
I want to model some sort of flowchart diagram. It can contain three kind of entities: choice, directory and file. The choice entry has two children (yes and no). The dir entry has one child and the file entry has no childs. All entries have a parent. The child on an entry can be any of the three entries. the parent can than of course also be any of the three.
To model this I've created an abstract chart element which contains the 'parent' relation and other common properties. I made subentries for the three non-abstract entries. In order to have the child of the dir entry point to any of the three subentries I made it's target the abstract entry. I did the same with the 'yes' and 'no' relations of the chioce entry.
Now I've the problem that Core Data does not understand the inverse relations: If I make the parent relation of the abstract entry point to itself than Xcode gives me a lot of warnings about missing inverse relations. I made an screenshot of what I've so far: http://beryllium.net/~remco/core-data.png
How can I make a child relation point to any of the three subentries while having a parent relation which also points to any of the three in such a way that Xcode/Core Data understands the inverse relations?

Thanks in advance,

Remco Poelstra

----

Do what XCode says: create the inverse relationships.  Basically, you want every relationship to have arrows on both ends.  There is a drop down menu to let you set the inverse relationship.  This is not strictly necessary, but everything is much happier if you do.

----

Thanks for your reply.
That's exactly the problem. I can't create the inverse relationships, because the targets won't match easily and I've only one parent relation while I've multiple targets..... If I make a parent relation for every child, than the model won't really fit the problem.

----

Ah, sorry, I understand what you mean properly now.  Well, it depends on what you actually want to accomplish.  If you are just interested in making the warnings go away, you can put in inverse relationships that are never used.  Then you could manually bypass the inverse relationship in your model classes.  When an object attempts to access its parent, return the parent object from the abstract class instead.  When a relationship is established, you would also have to manually mirror it to the parent relationship.

Alternatively, you could give your abstract class two 1-to-many relationships to itself, and manually manage which ones are valid in the children.

Another option would be to do away with the abstract classes' parent relationship and simply inverse the 3 other relationships to the abstract class.  You could then add a transient, undefined attribute to the abstract class which returns the parent object from the currently valid relationship.

I don't believe there is a way to have an relationship be the inverse of multiple relatiobships.

----

Thank you. So the only solutions there are involve some manual code to keep the object graph consistent? I hoped that that wasn't needed, but if I have to then I think I'll go with the first solution you gave.

