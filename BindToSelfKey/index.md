---
layout: page
title: BindToSelfKey
---



I recently discovered that you can bind to an object's "self" key.  I'm a little worried that I'm doing something dangerous or something?  Is binding to "self" a good idea?  I'm doing this in order to create an NSValueTransformer that transforms more than one attribute into a new composite attribute that's only useful as a view binding for a table column.  The technique likely has other uses.

I was expecting to have to create a "setSelf" method on my object class but I tried the binding without doing that and it worked right out of the box.  I don't see a setSelf: method on NSObject or NSManagedObject so I'm not sure how my object can be KVC-compliant for the key "self" already, but hey.  Not complaining.  But am I creating a possibility that my object could get a setSelf: message and commit suicide or something bad like that?

You can bind to an object's "self" key and then feed that into a value transformer if you need to create a value in your view that is dependent on more than one attribute in the object.  For example if you have "name" and "type" attributes on an object and you want to bind a table column in your view together so that it displays a column that uses an icon for each type.  You want a "nameWithIcon" column with an NSAttributedString so you want to use a value transformer to create that.  You can't bind a column to "name" and create a transformer because your transformer won't be able to access the "type" attribute for that object in order to figure out which icon to use.

I just encountered that exact scenario (names not even changed to protect any innocents) and binding to "self" is the solution.  I am in the process of factoring my entire model out of my application and into a framework.  I have Core Data entities that represent data items that go into NSTableView and NSOutlineView instances through controllers.  They have a string attribute "name" and another string attribute "type".  I used to have Core Data entities that were represented by Objective C classes in which I created a "nameWithIcon" key.  The [MyClass nameWithIcon] message handler would spit out an NSAttributedString that consisted of an icon image selected based on the "type" attribute, followed by text for the "name" attribute.

I wanted to remove the "nameWithIcon" method from the model class because that's really a view feature.  My model and views/controllers are in different bundles and I want all icon resources in the views/controllers main app or plugin bundles and not in the framework bundle that represents the model.  In my view/controllers bundle for my main app or a plug-in, I just bind a table column to "Controller Key: arrangedObjects", "Model Key Path: self" on my chosen NSTreeController.  Then I name the value transformer with "Value Transformer: ItemToNameWithIcon"  The value transformer itself is just a snippet of code that I drop into my AppController.h that looks like:

    
@interface ItemToNameWithIconTransformer: NSValueTransformer {}
@end
@implementation ItemToNameWithIconTransformer
+ (Class)transformedValueClass { return [NSString class]; }
+ (BOOL)allowsReverseTransformation { return NO; }
- (id)transformedValue:(id)item {
    return item valueForKey:@"name"] stringByAppendingString:[item valueForKey:@"type";
}
@end


You need to register your transformer(s) before any of your controllers need them.  I do that in [AppController initialize] like this:

    
+ (void)initialize {
    ItemToNameWithIconTransformer *nameTransformer = [[[ItemToNameWithIconTransformer
        alloc] init] autorelease];
    [NSValueTransformer setValueTransformer:nameTransformer
        forName:@"ItemToNameWithIcon"];
}


-endymion


----
The ability to bind to self is very mysterious. There is no such ivar, and no such method. So it must have been included in Apple's code for KVC.

In any case, having a setSelf: does not make much sense. How can an object remain the same and change its     self (sic)? I suppose in the above code, you never change self? It is "read-only", right?

With several ivars to combine, an alternative to NSValueTransformer is VirtualKeyValueCoding.

----

Right, my use for binding to self is always in a read-only context.

I agree that setSelf: doesn't make much sense but I'm used to writing setSomething: methods that don't make much sense just in order to create a KVC key.  I have a lot of code like:

    
- (void) setKnownSources { return; }
- (NSSet *) knownSources
{
    // Useful stuff..


Just as long as binding to self doesn't accidentally cross the streams and anger the gods somehow, I'm happy.

Thanks for the pointer to VirtualKeyValueCoding.  I'm off to investigate that now.  Always learning.

-endymion

