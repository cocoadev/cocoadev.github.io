---
layout: page
title: BindingsAndCoreDataQuestion
---




I have two entities specified in XCode{A, B}.  A has a 1 to 1
relationship with B, while B has 1 to many.  Now B(totalDistance) has
a property which is dependent on the sum of all the properties in each
A(distance).  Incidentally the Core Data Programming Guide: Core Data
FAQ section of the documentation has a  section which describes my
problem with a solution.  So I went ahead and implemented the
solution.  I added the method the following methods to B

    
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object
change:(NSDictionary *)change context:(void *)context
{

    if([keyPath isEqual:@"distance"])
    {
        NSLog(@"receiving notification that the distance changed");
        [self updateTotalDistance];
    }
}

- (void)updateTotalDistance
{
    NSNumber *totalDistance = [self valueForKeyPath:@"email@hiddennce"];
    [self setTotalDistance: [totalDistance floatValue]];
}

Now in the A entity I have the following:

- (void)setB:(NSManagedObject *)value
{
    [self willChangeValueForKey: @"b"];
    [self setPrimitiveValue: value
                     forKey: @"b"];
    [self didChangeValueForKey: @"b"];


    [self addObserver:[self b]
           forKeyPath:@"distance"
              options:(NSKeyValueObservingOptionNew)
              context:NULL];

}



So everything compiles just fine but when I run and I change the
distance in A I get the following error:

-observeValueForKeyPath:ofObject:change:context: only defined for
abstract class.  Define -[NSManagedObject
observeValueForKeyPath:ofObject:change:context:]!

Any ideas?  I usually try as hard to find a solution before posting
but I have been stuck on this one for a couple of days.

Thank You.

----

I might be wrong, but I think you actually want to be using dependent keys. See the KVO method     + (void)setKeys:(NSArray *)keys triggerChangeNotificationsForDependentKey:(NSString *)dependentKey covered here: file:///Developer/ADC%20Reference%20Library/documentation/Cocoa/Conceptual/KeyValueObserving/index.html

You would define dependent keys in your object's +initialize method.

----

According to the Core Data FAQ that will NOT work.  Apparently I have to use what I implemented.  What I a don't understand is why am am not receiving the message in the entity.

