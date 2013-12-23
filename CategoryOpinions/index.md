---
layout: page
title: CategoryOpinions
---

@discussion CategoryOpinions : WikiNode <ObjCLikeSyntaxing>

I love categories!

@end

@discussion CategoryOpinions(MyClasses)

They are possibly the most useful way of orginizing code after you've accepted OOP.  I generally put all of the categories for a class in a header, and have categories for my classes like: MyObject(IBActions), MyObject(NSTableViewDelegate), MyObject(NSComboBoxDataSource), MyObject(NSCodingConformance), MyObject(DOScriptingArchitecture) MyObject(PrivateMethods) etc. leaving only alloc/init/dealloc/awakeFromNib kinds of methods in the main class interface. 

@end

@discussion CategoriesAreGood(OtherPeoplesClasses)

Beyond using categories just to break up my class in a nicely readable parts, I occasionally add categories to classes in Cocoa for gathering an oft performed group of methods on it.  Things like NSString(WordAtIndex) etc.   Because of categories ClassClusters are possible... Without them you'd have to re-edit the class to return the instance of the new subclass each time you added one.  With Categories, the subclass can declare/define it's constructor in the superclass.

@end

//This page was written for people new to cocoa programming, as well as for me! I needed to let the world know that CategoriesAreGood!

