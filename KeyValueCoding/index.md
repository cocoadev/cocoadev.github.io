---
layout: page
title: KeyValueCoding
---




http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/index.html http://itshumour.blogspot.com/2011/07/funny-marriage-jokes.html

Key-Value coding is a protocol which is very easy to implement. It is based on a simple naming scheme for accessor and mutator methods. Accessors should have the same name as their associated instance variable, which should begin with a lower-case letter. Mutators should consist of the word "set", followed by the name of the instance variable with an initial capital. http://goo.gl/Cx9sQ

For example, for the instance variable http://goo.gl/OeSCu

    NSSize size,

the accessor should be

    - (NSSize)size;

and the mutator

    - (void)setSize:(NSSize)newSize;


----

Key-value coding seems to be best suited for automatically populating tables. It isn't versatile enough to act as a general accessor/mutator technique (as in AccessorMethods), as it doesn't allow for indices *(no longer true, see below)*. I *think* it's also good for serialization/archiving, but I'm not sure about that.  I'd like to hear where people have found key-value coding to be useful.

----
As you indicate, it is good for serialization:  You have a text file that contains instance data for flattened objects.  Say there are lots of objects with lots of attributes, and you plan to access only a few attributes for a few objects.  You want to deal with the data like objects, but not incur the overhead of building objects out of and re-serializing the whole file.

So you create a class with the key-value naming convention.  Say its a file of students, with studentID, name, age.  Psuedo-code for significant parts might look like this: http://www.thefunnyquotessayings.com/cool-hilarious-funny-quotes-sayings/

    
/* instance vars */

int studentID;     /* set this in the constructor */
string name;
int age;

char[MAX] bytes;    /* the serial representation of this object */
bool dirty = false;  /* set if the object is to be written */

/* accessor */

(int) age
{
    if (age == nil)
        age = self classOf: AGE] fromChars: [self bytesFor: AGE;

    return age;
}

/* setter */

(void) setAge: anInt
{
    age = anInt;
    dirty = true;
}


The     classOf: method accesses class data that associates the class of each instance variable with an identifier.  The     bytesFor: method first checks to see if bytes are initialized.  If not, the object's bytes are read from the file, based on the id.  (It often makes sense to build an index of the file in advance).

Once bytes are read,     bytesFor: gets the substring in bytes which describes the particular instance variable.  Together,     bytesFor: and     classOf: describe the serial format of the object.  The destructor checks to see if dirty == true, then reverses the process (flattens instance data) and writes bytes to the file.


Its assumed that the class of each instance variable implements a     fromChars: method.

----

Panther added a bunch of forms to KeyValueCoding. You can now implement methods like these (where "foobaz" is a scalar property and "foobazi" is an array property):


*foobaz
*isFoobaz
*setFoobaz:
*countOfFoobazi
*objectInFoobaziAtIndex:
*getFoobazi:range:
*insertObject:inFoobaziAtIndex:
*removeObjectFromFoobaziAtIndex:
*replaceObjectInFoobaziAtIndex:withObject:
*validateFoobaz:error:


**Is there nothing like     addObjectInFoobazi:?** - which would be equal to pass     countOfFoobazi as the     atIndex: argument.

----


To support AppleScript element access, you can use the following forms: http://www.thefunnyquotessayings.com/dr-sheldon-cooper-quotes-the-big-bang-theory/


*insertInFoobazi:
*insertInFoobazi:atIndex:
*removeFromFoobaziAtIndex:
*moveFoobazi:toIndex:

*(This is an undocumented form supporting NSMoveCommand. The first argument is the object to move.)*
*replaceInFoobazi:atIndex:
*valueInFoobaziAtIndex:

*(Never called. Use -objectInFoobaziAtIndex: instead.)*

**I just set a break point - it's called in 10.3**

*So did I. I stand by my statement.*
*valueInFoobaziWithName:
*valueInFoobaziWithUniqueID:


The AppleScript forms seem mostly redundant to the other forms. It's a real pain.

----

If you provide array accessors like **-foobazi** and **-setFoobazi:**, the indexed accessors will not be used.

----

To explain nested key references (or key paths), let's say we have the following classes, both of which use the default implementation of key-value coding:


***Foobaz**, which has an NSColor ivar called **color**.
***Container**, which has an NSArray ivar called **foobazi** containing instances of **Foobaz**.


The key "color" applied to a **Foobaz** will return the **color** ivar. That is, it will return an NSColor instance.

The key "foobazi" applied to a **Container** will return the **foobazi** ivar. That is, it will return an array of **Foobaz** instances.

The key "foobazi.color" applied to a **Container** will return the "color" key applied to each element returned by the "foobazi" key. That is, it will return an array of NSColor instances, where each element of the array is the **color** ivar of the corresponding **Foobaz** elements of the **foobazi** array.
The key "foobazi.@count" applied to a **Container** will return the number of elements in the **foobazi** array. See KeyValueCodingOperators.

----
These descriptions are less than clear. What is a Foobaz(i) and is the 'i' facilitating functionality? It is difficult to tell given the fact you are jumping between, using it, to not using it, without explanation.

----
Sorry about that. "foobaz" is a traditional nonsense name, and "foobazi" is probably the plural form. Object properties can be named whatever you like, and I simply chose to use "foobaz" as the name of a scalar (or to-one) property and "foobazi" as the name of an array (or to-many) property. I could as easily have used "document" and "documents", or "sun" and "rain". Cocoa does not care if the property names are related to each other, or if they are plural or not. Anyway, I've tried to clarify the above descriptions. -- DustinVoss

----
Thanks for clearing that up for me. I am an old-school C/C++ type and I suspected Foobaz(i) was analogous to what I traditionally call foo, bar, foobar and last but not least, fubar. The last, in this case, represented my understanding of Foobaz(i) before your explanation.

It would probably be much clearer if you used an example with a more common plural form, e.g. "foobar", "foobars".

----
**"foobaz" is a traditional nonsense name**: It is nothing of the sort! For traditional nonsense names try foobar and foobars as the person above suggested: http://en.wikipedia.org/wiki/Foobar http://itshumour.blogspot.com/2010/06/twenty-hilarious-funny-quotes.html

----

*Q:* If I create a class:
    
@interface MyClass : NSObject
{
   NSString* someObject;
}
@end

And bind e.g., the content value of an NSTextField to someObject (without providing a setSomeObject: implementation) then ObjectiveC will automatically access my instance variable -- my question is, will ObjectiveC release the old ivar and retain/copy the new one?

----
*A:* Yes, see the description of **setValue:forKey:** at file:///Developer/Documentation/Cocoa/Reference/Foundation/ObjC_classic/Protocols/NSKeyValueCoding.html
 --DustinVoss
----

*Q:* What are the method signatures needed on Java classes? That is, instead of insertObject:inFoobaziAtIndex: do we need 'public void insertObjectInFoobaziAtIndex(Foobaz foobaz, int index)'?

*Q* Is there a way of using a key path to reference an element of an array? Say I have the property "entries" which holds three strings and I want to get at the second string. Intuitively I'd have thought: 'entries[1]' - but that doesn't work. Is there a way to do this? --CliveJevons

----
Here's a category that will let you do it using a key path like 'entries.@1'.  --JimSpeth
    
@implementation NSArray (JSArrayIndexedKVC)
- (id)valueForUndefinedKey:(NSString *)key
{
    int index;
    if ([[NSScanner scannerWithString:key] scanInt:&index])
        if (index < [self count])
            return [self objectAtIndex:index];
    return [super valueForUndefinedKey:key];
}
@end

----

----

Storing a float array in a keyed archive (sample code):  http://www.johankool.nl/mac-articles/cocoa-sample-code-storing-a-float-array-in-a-keyed-archive

----
I suspect you want NSCoding, not KeyValueCoding, for that sample code.

Also, that sample code will fail horribly if the archived data is moved between architectures. It should not be taken as a template to follow.

----

*Q:* Is there an easy way to have a notification sent if any property of an object changes?  The best I've come up with is having a dummy BOOL hasChanged, setting dependencies in the class initialize method and having -hasChanged always return YES.

