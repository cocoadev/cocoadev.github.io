---
layout: page
title: FunctorPatternDesignAnalysis
---

Editor's Note: This lengthy design-related discussion, moved from an ill-named initial page, eventually settled on using the GangOfFour FunctorPattern. It contains a lot of detail about the specifics of the problem, but is educational.

----

I want to design an object that does a variety of math and statistical operations on an array of numeric data. Data sets are NSArrays with all data arrays being the same length. Operations include:

* trigonometric functions on values in a single array: sine, cosine, and tangent functions (take two arguments: the name of the data array and the type of trig function to perform), 

* math functions on values in a single array with a second value as a number or as a number in a second array at the same index and a third input of math function type: power (x^y where x is the value in the data set, ratio (division x/y), root (x^(1/y)) (takes two arguments: the name of the data array as x and the second value for y or a second data array as y), and 

* operations comparing multiple values at the same index in each array across arrays: maximum, minimum, mean (takes two or more data array arguments plus the type of comparison)

I would like to be able to call these functions from any of the following: a command interface, a form window, or from AppleScript. But to me this shouldn�t make any difference. It is a view thing that doesn�t affect the model. However, practically it seems method 2 below is the best approach since it would allow set up the analysis objects via accessor methods for the most flexibility, even though it seems a much more complicated approach.

So for designing the analysis object I don�t know whether to design it as an object like so in method 1:?
    
@interface PRDataModel : NSObject {
	NSDictionary* dataSets; //Data NSArrays are stored with their key being an NSString description, with all data arrays being the same length
}
//Accessor methods
-(NSArray*)resultOfTrigFunction:(NSString*)function onDataSet:(NSString*)dataSetName;
-(NSArray*)resultOfMathFunction:(NSString*)function onDataSet:(NSString*)dataSetName secondValue:(NSNumber*)value;
-(NSArray*)resultOfMultipleFunction:(NSString*)function onDataSets:(NSArray*)dataSetNames;
@end

or method 2:
    
@interface PRDataModel : NSObject {
	NSDictionary* dataSets; //Data NSArrays are stored with their key being an NSString description, with all data arrays being the same length
	id function; //Function as an object of type PRTrigFunction or PRMathFunction or PRMultipleFunction
}
//Accessor methods
-(void)setFunction:(NSString*)functionType; 
-(NSArray*)performFunction;
@end

@interface PRTrigFunction : NSObject {
	NSArray* dataSet;
	NSString* function;
}
//Accessor methods
-(NSArray*)newArrayFromCalculation
@end

@interface PRMathFunction : PRTrigFunction {
	NSNumber* powerValue;
	NSArray* powerArray;
}
//Accessor methods
-(NSArray*)newArrayFromCalculation
@end

@interface PRMultipleFunction : PRTrigFunction {
	NSArray* otherArrays;
}
//Accessor methods
-(NSArray*)newArrayFromCalculation
@end


or method 3 which would be to make an object for each analysis type of sine, cosine, tangent, power, etc?

Is one of these methods advantageous over the others? Is there a better approach I haven�t thought of?

Thanks for all the input!

----

There isn't much context around this design, so it's hard to make any judgements. Based on what I see,  there really isn't any value in turning all of these mathematical functions into classes at all. They are simply that - functions. They really don't exhibit any behavior or change state - other than to be assigned their data sets. If your application is really about data sets and mathematical functions - well then, option 3 looks better, but design is really all about overall context.

A good starting point for getting your design started is to explain to us what functionality the user needs. Don't talk about windows, buttons, text fields, or classes or objects. Just describe what the user wants to be able to do. Examples:

*My wife has trouble organizing her summer activities with the  children. Ordinary calendaring tools aren't specific enough, because she'd like to keep information about educational games, when she last played them with the kids, what equipment she might need, and similar information. Then she can more easily schedule activities, and get a shopping list for equipment.*

--TimHart

----

Thank you, Tim, for your input. 

This is a scientific application based on an old text book I found for cartographic modeling. It treats each map layer as a mathematical variable that can be operated on by math functions. 

Example syntax for command line is: NEWLAYER = Function FIRSTLAYER [and NEXTLAYER] etc.

The user has an image that represents a map, the map is several layers each stored as an array of values (integer or double) that represents each location. The user wants to manipulate the image layers to get information from the map. Layers in the image might be something like:

* Elevation = feet high
* LandCover: Forest = 3, Grass = 2, Water = 1
* Roads: Interstate = 3, Highway = 2, Local Road = 1
* Population = number of people in neighborhood


Now the user wants to find out certain things about the map:

* InterstateBridges: Interstate = where Roads == 3, Water = where LandCover == 1, InterstateBridges = Product Interstate and Water
* Percent of population above 500 feet: HigherElevation = where Elevation >= 500, Population500 = Product Population and HigherElevation, SumPop = Sum Population, SumPopulation500 = Sum Population500, Percent500 = Ratio SumPop500 SumPop


In plain C this would just be a simple parser and a call to the correct function with variables. I have done this before. However, this doesn't seem to be the way in an object-oriented environment when I want to use a form window or use AppleScript. In the future I would even like to be able to develop the models in a graphical flow-chart way. But this is really different thinking than what I've done before.

After trying some test cases right now I am leaning towards method 3 because it seems to emulate best the Objective-C tutorials I've seen and practised. 

----

Grrr - my brain is fried. Too much programming in Java today...

This section is written **after** the section below. I think it's more relevant. Whoever edits this page later is welcome to delete my entire brain-addled rant below unless someone pipes up that it was useful...

Don't forget about Objective-C categories. If you're well ahead of my stuff below - and already have good model design, categories let you do this:

So if you needed the square root of all the members of an array:

    

@implementation NSArray (PRArrayMath)

-(NSArray*) sqrtOfMembers
{
    NSMutableArray *result = [ NSMutableArray arrayWithCapacity:[ self count ] ];

    NSEnumerator *iter = [ self objectEnumerator ];
    NSNumber *number;
    while( number = [ iter nextObject ] )
    {
        [ result addObject:[ NSNumber numberWithFloat:sqrt( [ number floatValue ] ) ] ];
    }

    return result;
}
@end

/* and to use the code */

NSArray *myDataSet;//assume this exists
NSArray *mySqrts = [ myDataSet sqrtOfMembers ];




Now wasn't that a lot shorter than my rant below? :P

--TimHart
----

Context helps a lot!

Basic user descriptions suggest that you need a Map class and a Layer class. An instance of a Map class owns a collection of Layer instances. I didn't quite understand from your description above if an example of a Layer would be an 'Elevation' layer, or if a Layer would be 'Milwaukee', with an elevation, land cover, etc. I'll assume the former. Let me know if I'm wrong.

    

@interface PRLayer : NSObject
{
    // define information common to all layers 
}

// behavior common to all layers, 
//including the mathematical functions

@end

@interface PRElevationLayer : PRLayer
{
    // is there information unique to elevation 
    //that wouldn't exist for other layer types? 
}

// is there behavior unique to elevation that 
// wouldn't exist for other layer types? 

@end 

// similar definitions of other layer types

@interface PRMap : NSObject
{
    NSDictionary *layers;//I'm suggesting a dictionary in case you want
                                    //to do something later like PRLayer *layer = [ layers objectForKey:@"elevation" ];
                                    //but you may not need or want to do that.
}

-( PRMap* ) submapMatchingQuery:( PRMapQuery* );

@end

@interface PRMapQuery : NSObject
{
   //This class exists to describe what the
   //user is trying to find on the map
}

@end




My code above may be nothing close to what you need, but it does satisfy your description:

A user has a **map** represented in several **layers**. The user needs to **query** the map in order to find specific locations. I don't know enough about the representation of the model to know if a location is the same kind of thing as a 'zoomed in map' or if it really does have different attributes. Maybe location should be a subclass of map?

Of course this is a HUGE departure from your original question - regarding all sorts of mathematical functions being performed on data sets. But one of the goals of OO is to make the code simple to read and maintain. The code should describe pretty much what the user is trying to do:

    

PRMap *tennessee;//assume this is initialized somewhere

PRMapQuery *query = [ PRMapQuery mapQueryForPopulation:30 andElevation:800 ];
//there are much better ways to build querys - a queryBuilder class would work nicely
PRMap *city = [ tennessee subMapMatchingQuery:query ];



Yeah - this is a bit long-winded, and maybe it's off course already, but your initial design seemed to be a bit too close to traditional procedural problems, and I wanted to present a different way of thinking. I know we still haven't mentioned any math. The point is we're encapsulating information - solving the problem bit by bit. Each small snippet is readable, and makes sense to someone who doesn't understand all the math at once. A possible implementation of subMapMatchingQuery:

    

-(PRMap*) submapMatchingQuery:(PRMapQuery*)
{
    NSEnumerator *iter = [ layers objectEnumerator ];
    NSLayer *layer;

    PRMap *submap = [ PRMap map ];
    while(layer = [ iter nextObject ] )
    {
        if([ query isSatisfiedByLayer:layer ])
            [ submap addLayer:layer ];
    }
}



... and isSatisfiedByLayer might look like this:

    

-(BOOL) isSatisfiedByLayer:(PRLayer*)layer
{
    return [ self expectedValue ] == [ layer fancyMath ];
}



Sometimes C algorithms translate poorly to OO design. Sometimes you can create a relatively clean OO design once you know the algorithms ( which I'm missing, of course ). I don't know why you'd need trigonometric functions to query all your 'elevation' layers to find an elevation that is 800 ft - or higher, etc... but I then again I have no education in computer aided cartography or CAD. The point is the only code that has to worry about the math is the query - because it knows what it's looking for, and the layer - because it has all the data necessary to do the calculation. The rest of the code can look pretty much the way your user might describe what they are trying to do.

--TimHart

----

Thank you, Tim!

As I sit and ponder the above, both approaches Tim suggested are excellent. I am going with Map class and Layer class rather than the class categories. Class categories will assume data is always a raster or grid structure stored as an NSArray. In the future I want to experiment with quad-tree and vector based spatial layers. In this case Layer classes can be of 3 types. The class system seems more adaptable.

To answer Tim's question in the miniscule case anybody reads this and is interested, 

*"Trigonometric functions can be used in cartographic modeling to deal with angular measurements such as those associated with compass directions, topographic relationships, lines of sight, and cartographic projection systems. These  functions are also used to characterize cyclical variations over time and/or space such as those associated with climatic patterns, geomorphological processes, and biological or social activity." Tomlin 1990. Geographic Information Systems and Cartographic Modeling.*

----

You're welcome.

Remember that in Object Oriented languages, polymorphism is a Very Nice Thing (tm). You can certainly use categories, as long as  your message is generic enough to work in all of them. In other words, you can implement 'sqrtOfMembers' for each of  NSArray, PRQuadTree, PRVector, PRLayer or id<sqrtAble>. As long as you have a category for each that implements 'sqrtOfMembers', the calling code can be written so that it doesn't care WHAT type the data is in - only that it knows how to perform the requested task.

--TimHart

----

And the really cool thing is, you could use that exact same method on any object that responds appropriately to -objectEnumerator. You'd want to check whether the objects in it responded to floatValue though.

----

*TheoHultberg*: The first thing that crossed my mind when I read this page was "functor". See FunctorPattern.

It seems that your syntax allows for a couple of different algorithm types, the FunctorPattern would encapsulate one kind, that which applies an algorithm on all values in an array (array -> array), you could perhaps call it a filter or a transform. In your problem description above there is also the type which takes an array and returns one value (array -> number), like max, min and mean. The pattern can easily be used to create other kinds of algorithm types, but the objects of the different types would not be interchangeable since the return types of the apply-method would be different.

----

**UPDATE: 4-2-2005**

In the end I went with functors. They allowed me to daisy-chain processing elements in any way I wanted. Some algorithms use one input arrray, some multiple arrays, some using one input and creating seveal inputs. By passing an NSArray of my objects I can do a batch process using algorithms requiring only one input, or do a single process using multiple inputs. I can remain blissfully ignorant of which it is doing and still have the same input and output data type (NSArray). This came out of the idea supporting NSArray and NSEnumerator. I will most likely change this to passing NSDictionary so that I can keep track of the name of the input array when doing batch processing and also know which array is what when creating multiple arrays from a single input (case: a cost accumulation surface would create the actual accumulated cost surface from points on the map, plus a surface of directions to get there).

Thanks to everybody who helped. Your responses gave me a lot to think about and come up with a better design than I could have alone.

