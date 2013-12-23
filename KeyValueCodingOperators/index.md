---
layout: page
title: KeyValueCodingOperators
---



This topic is related to NSController.

Apple's documentation lists a set of array operators for use in KeyValueCoding.

http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/Concepts/ArrayOperators.html#//apple_ref/doc/uid/20002176

For instance

"@sum.amounts"

would return the sum of the amounts fields on all objects in the currently considered array.

Do any other operators exist?

Do any non-array operators exist?

Is it possible to create new operators?

For instance; could I write an operator to return the proportion the current record's field is of the sum of that field over all records (sorry, dropped into database language there)?

�I haven't heard of a way to make new operators. And I have no idea what you just said. :)

*I believe that     @amounts will return the number of items in that array, will it not?*

�There is no     @amounts, but     @count will return the number of items in an array.

*I meant,     @x where     x is an array name. I've seen that referenced elsewhere on this site, is all.*

----

For the sum of a given field (for example, 'amount'), use the key path @sum.amount

However, there used to be a way of defining new aggregates, but not any more, it seems.  In the good old days of EnterpriseObjectsFramework, NSArray didn't implement KVC.  It was implemented in EOControl.framework as a category on NSArray, which provided the following semantics (ripped from the WebObjects 4.5 docs):

*

valueForKey:

� (id)valueForKey:(NSString *)key

When passed a �normal� key, returns an array composed of the results of sending valueForKey:
to all elements of the array. When passed a key prefixed with �@�, returns a single value that is
the result of invoking an aggregate function on the values of the array.
For instance, if this method were passed the key @sum.budget, it would invoke
computeSumForKey:@�budget� on the array, which would add the values for the budget keys for
all of the objects in the array. The returned value would be the sum of all of the objects� budgets.
The following aggregates are defined: @sum, @count, @avg, @min, @max. You can extend this
set by adding methods to NSArray of the form computeNameForKey:.

*

This has changed since Panther now implements -valueForKey: and -valueForKeyPath: on NSArray, and doesn't respect the old semantic (despite the fact the ObjC version of EOControl installed in /System/Library/PrivateFrameworks by WO5.2.x does...).  The new version uses -_countForKeyPath: -_avgForKeyPath: etc instead.  Simply implementing -_myKeyForKeyPath: does not work.

It's possible you could swizzle -valueforKey: and -valueForKeyPath: to re-implement the old semantic.  I'll be trying that soon, as the new way of doing things breaks my code... 

TufTy

----

Panther's implementation is definitely busted.  I just tried compiling and running eogenerator, and it now throws all sorts of exceptions (it depends on exactly the functionality above).  This also, of course, busts my product, which also relies on the above functionality, which makes for unhappy customers.  Bah.

MethodSwizzling works, but you need to be careful if you're dealing with the case where the same selector is defined in more than one category (In my case I have NSArray(KeyValueCoding) and NSArray(TCUKeyValueCoding) or NSArray(EOKeyValueCoding)) - I don't think you can be _sure_ what the registered IMP is at any given time, as it will all depend on library loading order.  For example, my 10.3 built eogenerator raises exceptions, but my 10.2 version doesn't (it's broken in other ways, though).  I'm trying a really hairy swizzle that goes through the runtime and pulls all the IMPs for a given selector (in my case, valueForKey: et al) and then tries to run through them in order, catching any exceptions except for the final one, in which case it will raise.  But even that leaves me with a nasty taste in my mouth.  I'm really not sure why Apple broke the existing semantic.

TufTy

----

Has anyone come up with a good way of persisting the model used in bindings to a database? Currently I'm playing around with a test project where I have an array of data items where each one tracks whethere it has been changed since it was loaded from the database. This is done by providing a setXXX: method for KVC and to update a BOOL flag held on the class therein. When I then trigger my persist function I can check each to see if it has changed - needs update, doesn't have an ID - needs inserting, or neither - doesn't need anything. Then I have to load a list of all existing IDs from the DB and check for ones that exist on the DB but not in the app - these need deleting. Its sure to work - but seems a bit clutzy. I haven't come up with anything better myself yet - any of you guys have a nice solution to this?

-- CliveJevons

----

Not sure I fully grasp the magnitude of the problem, but to be KVC-compliant you only need to implement     setValue:forKey: and     valueForKey:, so you could do something like below.

--AllanOdgaard

    
@interface DBWrapper
{
   NSMutableDictionary* localValues;
   NSMutableDictionary* dbValues;
}
- (id)valueForKey:(NSString*)aKey;
- (void)setValue:(id)aValue forKey:(NSString*)aKey;
- (BOOL)persist;
@end

@implementation DBWrapper
- (id)valueForKey:(NSString*)aKey
{
   id res;
   if(res = [localValues objectForKey:aKey])
      ;
   else if(res = [dbValues objectForKey:aKey])
      ;
   else
   {
      res = fetch_from_database(aKey);
      [dbValues setObject:res forKey:aKey];
   }
   return res;
}

- (void)setValue:(id)aValue forKey:(NSString*)aKey
{
   [localValues setObject:aValue forKey:aKey];
}

- (BOOL)persist
{
   // localValues needs to go to database
   ...
}
@end


----

That's certainly a very excellent and elegant solution to one of the core problems involved - the tracking of changes to an 'entity' during the application's life cycle. From the information gathered during an entities life cycle using the DBWrapper, a sub-class can generate the necessary INSERT and UPDATE statements. I wouldn't, however, use the fetch_from_database(key) at that stage. That would result in a separate DB access for each and every property for the system, which would kill the performance beyond belief :) Especially if the host the DB resides on is not the same as the one the app is running on. A fair amount of foot work is still involved, though - I'd still have to sub class the wrapper and then code the knowledge of the DB structure into that class so that I can form the INSERT, UPDATE and DELETE statements. But I've got a feeling I'll have to do that anyway. I could conceive a system which relied on generation to take the boredom out of this. But I don't think I'll be writing one for the little app I'm thinking of - but if I change my mind and do, I'll post it here :) I'll also still have to load in the list of IDs from the database to check whether I need to perform any deletes on the entities.
I've been toying with the idea of perhaps shifting some of the logic to the DB in stored proceedures and the app simply sends over all the data so that the DB can then decide on how to persist the data. But that's just an idea and I haven't thought that through enough yet.
BTW: I haven't yet thought through any DB locking yet, but the app I'm thinking of won't need that, because it'll be only one person (me) using it. The reason I need the data in a DB is that I am collating data which will then be used by other systems for further processing - and they won't necessarily run on Cocoa or even a Mac.

Anyway - I'm really liking your solution to the tracking of changes problem, Allan :) **Thanks v.v.v.v.much for the input ;D**

--CliveJevons

----

Is it possible to create your own custom KeyValueCodingOperators, e.g. for arrays?

For example, I'd like to be able to get a count of a certain value, ie. Count of Status=2, can i create something like "arrangedObjects.properties.@StatusTwo.status" ?

----

I can't remember if this was ever official, but basically the convention is that the array calls own methods for @-keys and methods on each contained element for normal keys.

So you'd just have to implement a method on the array or overload valueFor(Undefined)Key: to add new operators.

If you do:
    
[array valueForKey:@"@count"];

It translates into:
    
[array count];


Whereas, if you do:
    
[array valueForKey:@"count"];

It translates into (ignoring here wrapping primitive types in objects etc.):
    
NSMutableArray* res = [NSMutableArray array];
for(unsigned i = 0; i != [array count]; i++)
   [res addObject:array objectAtIndex:i] count;


So with that knowledge you can do:
    
@implementation NSArray (MyOperator)
- (int)theMeaningOfLife {
   return 42;
}
@end


And now you can do:
    
[array valueForKey:@"@theMeaningOfLife"];


But if you want to do dynamic operators, like addOne, addTwo etc., you'd need to subclass the array and instead implement the valueFor(Undefined)Key: to select the proper method. So that wouldn't really work for existing arrays, only your array subclass (but actually, you can't really subclass an array, since it's a ClassCluster).

