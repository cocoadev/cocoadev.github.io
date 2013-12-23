---
layout: page
title: NSValueTransformer
---




NSValueTransformer is described nicely here:
http://developer.apple.com/documentation/Cocoa/Conceptual/ValueTransformers/index.html

Note, that you have to register the transformer, as explained in the above link, in order for it to work with bindings. The name you use for registering is what you'll use inside Interface Builder when doing binding.  *This does not appear to be true in IB3; you can just give it the name of a class and it will instantiate it.  Is this behavior documented anywhere, or did I get lucky?* http://goo.gl/OeSCu

When you use a value transformer on a view, the -transformedValue method is used when setting the view's state, and the -reverseTransformedValue method (if applicable) is used when reading its state. For example, if you have a checkbox which is bound to a user default through a value transformer, the -transformedValue method will be used when the checkbox is set from the default, and the -reverseTransformedValue method will be used when the default is set from the checkbox.

----

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


----
I'm sure this is obvious to every one else, but by setting the Controller Key of a binding to self instead of
the Model Key Path you can create ValueTransformers based on the Container Object e.g. the NSArrayController.

For example, I needed to enable a detail drop down list based on it the master records position in an sorted table, 
but only when there were more than three master records. 

So I set the drop down's enabled binding to...

Bind To: GenomeArrayController
Controller Key: self

Model Key Path was not set

The ValueTransformer transformedValue: method was... 

    
- (id)transformedValue:(id)value {

	if ([value selectionIndex] > 0 && value arrangedObjects] count] > 3) {
		return [[[NSNumber numberWithBool:YES];
	} 
	
	return[NSNumber numberWithBool:NO];

}


----
Regarding above post, If you are using NSObjectControllers, you can use "selection" instead of "self". The downside to this is that this causes invocations for every change to the selection.


----

----

Has anyone got a list of public domain NSValueTransformer classes?


* http://homepage.mac.com/oscarmv/OMVFPValueTransformers.sitx
 
 * Math and transform sequences
 
*http://www.cocoalab.com/cocoalab/developer/classes/CLValueTransformers.zip
 *CoreData faults <-> bool
 * points <-> other units
 * NSString <-> NSNumber
 * Prefix numbers with 0
 * radians <-> degrees
 * NSCalendarDate <-> NSString 
 * SMTE time <-> decimal seconds
 
* Here on CocoaDev
 
 * BzipValueTransformer (de)compresses NSString<nowiki/>s on the fly
 * JpegTwoThousandValueTransformer (de)compresses NSImages<nowiki/>s on the fly
 


A nice tutorial here on Tutorial: Cocoa Bindings and Value Transformers

http://macresearch.org/tutorial_cocoa_bindings_and_value_transformers

