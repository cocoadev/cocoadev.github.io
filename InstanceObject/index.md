---
layout: page
title: InstanceObject
---



A single instance of AnObject. An InstanceObject is commonly created by sending alloc and init messages to a dynamically or statically typed object pointer, or by calling a ClassMethod which handles creation of objects.

Where a class *defines* an object's capabilities, an *instance* is an actual living, breathing object of that class that is capable of performing the capabilities laid out by the class.

A typical way to create an object is like this:

Object* myObject = Object alloc] init];

Notice that first an "alloc" message is sent to the Object class (NOT an object of the Object class, but the class itself.)  This is known as a [[ClassMethod.  alloc reserves memory for the object, but does not initialize it.

The chunk of memory returned by alloc is then sent the "init" message, which performs the actual initialization of the object to its initial state.

An instance is analogous to a product that a real world factory would make. An instance/product is an independent entity. The features that give an instance the ability to behave as an independent entity are internal components called instance variables. Instance variables define the state of an instance.

The state of an instance is similar to the state of any real world object. Cars in a parking lot all have varying states of existence. One car might have a quarter tank of gas while another car might have a full tank of gas. One car might be blue while another car might be red. The traits of an object that vary from object to object are the traits that distinguish one object from another. Instance variables act kind of like the distinguishing traits of a real world object. Instance variables give an "instance" the ability to behave different from instances of the same class. A car modeled in Objective C could look something like this:

    

@interface CarObject : NSObject {
	int colorInstanceVariable;
	int numberOfDoorsInstanceVariable;
	int horsePowerInstanceVariable;
}



You could design an "instance" without instance variables, but that would be kind of pointless because one instance of a class would not have the ability to behave any different from another instance of the same class. 

After you have decided what kind of traits distinguish one instance from another and after you have defined the instance variables that represent these distinguishing traits, the next step (no pun intended) is to define methods that manipulate the traits of an instance and methods that inform other objects the current state of these traits (you can always paint a car another color during the lifetime of the car).

The methods that allow this type of interaction are declared in the interface of the object:

    

@interface CarObject : NSObject {
	int colorInstanceVariable;
	int numberOfDoorsInstanceVariable;
	int horsePowerInstanceVariable;
}

-(void)setColor:(int)_colorInstanceVariable;
-(int)color;
-(void)setDoorCount:(int)_doorCount;
-(int)doorCount;
-(void)setHorsePower:(int)_horsePower;
-(int)horsePower;

@end


   
The minus sign before each method is there to declare that a method is an InstanceMethod (A Method with a plus sign is a ClassMethod). Many Cocoa beginners will ask about PlusAndMinusMethod syntax. The difference is subtle but very important. 

The difference is between an -instanceMethod and a +classMethod is to remember what a ClassObject is and what an InstanceObject is. (A ClassObject is the "factory" that manufactures products called "instances" of the class). There is only one ClassObject for each class. You, as a programmer, may never have to worry about creating any factories for any of the classes you use. This can be demonstrated by the simple program below:
 
    
#import <Cocoa/Cocoa.h>

void main() {
	id stringInstanceObject= [NSString stringWithString:@"hello world!"];
	NSLog(stringInstanceObject);
}



"NSString" in the first line is a full fledged object, therefore you can send messages to it and get returned vales from it. Many people get confused about this point because "NSString" was not allocated, initialized or declared anywhere explicitly within the bounds of the program main(). Nothing magical is happening here, the runtime system is setting up everything for you. "NSString" is the Class Object (factory) for the class NSString. Anytime a class name is the first element inside brackets [ ] the class name represents the Class Object. In this case, the class name "NSString" is representing the Class Object of NSString. As I mentioned, "NSString" was not allocated or initialized. This is because a ClassObject is always available anytime anywhere in your programs once it is defined.  

A ClassObject can only perform this or that +classMethod, therefore you cannot ask a ClassObject to perform an -instanceMethod. On the same line of thought, a +classMethod can only be called by asking a ClassObject to perform it (an instance cannot perform a +classMethod). The ClassObject "NSString" was sent the message stringWithString:@"hello world!", therefore +stringWithString: is a +classMethod. The ClassObject "NSString", after receiving the message stringWithString:@"hello world!", manufactures an instance of NSString and sets the initial state of the instance to the value of "hello world!". 

The important thing to get from the paragraph above is that the main role of a ClassObject (factory) is to manufacuture instances (products) of the class. The Cocoa documentation will describe each +classMethod that can manufacture an instance of a class. The two most important types of +classMethod are allocation and "factory" methods. 

Allocation methods start with "alloc" (i.e. for the NSString class some of the allocation methods are alloc, allocWithZone:). Allocation methods allocate the memory needed to store the state (values of the instance variables) for a new intances of a class. Allocation methods are kind of like factory orders placed to produce generic instances of a class. These generic instances are all the same, in that aside from the serial number of the instance (called the isa number) you cannot tell the difference between one generic instance from another generic instance of the same class. Generic instances can be customized at the factory. The process of customizing an instance at the factory is call the initialization process (kind of like a custom factory order). Custom orders involve allocating a generic instance and then initializing the generic instance to a custom state. Newly allocated object are asked to perfom their first acts of customization at the factory. This initial act of customization is done by asking newly allocated instances to perform an initialization method. Since instances are asked to perform initialization methods, initialization methods are actually -instanceMethods not +classMethods. More about -instanceMethods later. The main thing to get here is that all instances of a class are created at the ClassObject "factory". These instances are created by palcing a "factory order" (asking the ClassObject to perform an allocation method). The process of sending a message that asks the ClassObject (factory) to perform an allocation method (FactoryMethod) is analogous to sending a factory an order for a generic uncustomized version of the product the factory produces.

The act of placing a factory order also involves some form of customization. The ClassObject (factory) is asked to allocate (manufacure) an instance of the class, but the instance is not ready for use yet. The instance needs to be customized first before it leaves the factory, but first I want to talk about a special custom order, the custom order for an uncustomized instance. 

An uncustomized instance actually involves some form of customization. This might sound vague, but Objective C provides a means to define explicitly how generic an instance can be (kind of like headquarters telling a factory that all generic cars coming off of the assembly line must be painted white). Subclasses of NSObject are permitted to override the generic initialization method:

    

-(id)init {
    self=[super init];
    if (self) {
    
        colorInstanceVariable=0; 
        
         // 0=white, 1=red, 2=blue, 3=green, 4=yellow, 5=gray

    }
    return self;
}




The generic initialization method "init" is how headquarters tells the factory that all generic cars are to be painted white. But what fun is there in defining how generic a generic instance is. Here is where initialization methods with arguments come into play.

Some of the most important -instanceMethods are initializer methods.  Initializer methods start with "init" (i.e. for the NSString class some of the initializer methods are init, initWithString:, intWithCString:). Initializer methods initializes the state (sets the initial values of the instance variables) of a newly created instance at the factory. Remember the idea of a "factory custom order"? Initializer methods are analogous to placing a custom order at the factory. The reaon I keep saying that initialization takes place at the factory is to drive in the point that initialization occurs only once. This is not saying that you cannot alter the state of an instance after it has left the factory, but this IS saying that you cannot altering the state of an instance after it has left the factory with another initialization method. Again, Initialization methods are only to be performed once. 

So how do you customize an instance at the factory? The Cocoa documentation reveals the initialization methods for each class. Any method that begins with "init" is an -instanceMethod that customizes newly allocated/created instances at the factory. The following is a message to customize an NSString with the text "hello world!". 

id helloWorldString=[[NSString alloc] initWithString:@"hello world!"];

"NSString" is the Class Object, "alloc" is the factory order for a generic instance and "initWithString:" is the factory customization request to initialize/customize the instance with the value of "hello world". The variable "helloWorldString" is used to reference the newly allocated and initialized instance. This line of code is basically an **explicit factory order**.  As simple as this **explicit factory order** may seem, many sources of confusion can come about when the topic of memory management or type declaration rears it's ugly head (this is another subject entirely). I only mention this because this line of code can also be written:

NSString *helloWorldString=[[NSString alloc] initWithString:@"hello world!"];

"NSString" shows up twice in this line. The first occurrence is just a type declaration and not to be confused with the Class Object. The second occurrence of "NSString" represents the Class Object and behaves like an object. An easy way to tell if a class name (i.e. "NSString") is acting as the Class Object or acting as a type declaration is to take note of where the class name appears in the code. If the class name is the first element inside brackets [ ] then the class name "NSString" is acting as the Class Object. If the class name "NSString" is outside of brackets [ ], than the class name is not acting as the Class Object (most likely the appearence of the class name outside of brackets [ ] is an act of type declaration). This simple line of code can be simplified further by using the second important type of +classMethod. The first kind of +classMethod was the allocation method, and the second important type of +classMethod is the factory/convience method.

id helloWorldString=[NSString stringWithString:@"hello world!"];

This line declares the variable "helloWorldString" of type "id" (the type "id" is a pointer/reference to an "instance" of the class NSString). "NSString" is being asked to perform the factory method "stringWithString:". This message does the following:



*allocate a new instance (ask the factory to manufacture a generic NSString object)
*initialize the new instance to the value of "hello world!" (ask the factory to customize the generic NSString with the value "hello world!")
*register the new instance with the runtime system to let the memory manager know that it needs to keep track of this new instance (see MemoryManagement)
*return the memory location of the object



Cocoa applications rely on the ClassObject (a Class Factory) and the InstanceObject (a products manufactured by a Class Factory). An object (ClassObject or InstanceObject) communicates by sending messages. Communicating with a ClassObject is done by asking the ClassObject to perform a ClassMethod. Communicating with an InstanceObject is done by asking the InstanceObject to perform an InstanceMethod.   

To recap, an InstanceObject is the product of a ClassObject. There is only one Class Object for each class, but a ClassObject (factory) can produce many copies of an InstanceObject. The features/traits/state of instances are stored in instance variables. Instance variables allow each copy of an InstanceObject of a particular class to have unique properties. **Implicit factory orders** and **explicit factory orders** are used to obtain an InstanceObject from a ClassObject. Communicating with an InstanceObject is done by asking the InstanceObject to perform an InstanceMethod. 

--zootbobbalu

----

How 'bout:

To recap, an InstanceObject is just an object you interact with by sending it messages. Many instance objects can belong to one class, which is overseen by a ClassObject. The class dictates what messages an InstanceObject can respond to, and what kind of instance variables it can have. The ClassObject is mainly responsible for producing a new InstanceObject.

-- KritTer, impressed by the article zoot has produced

----

thanks for the help, I'm going to work on the recap some more. It looks like I should update the article on ClassObject too, but I have to balance my time out. 

--zootbobbalu

Hey, I only just rewrote ClassObject! No fair! -- KritTer :)

----

This text was extremely helpful, thanks, but I still have one doubt. When you use self = [super init]; aren't you like overriding your class instance with the super class itself? And, why does it work the same when you don't set selft to anything?

----

Don't start!  :-) This heated controversy is well-discussed under FactoryMethod.

