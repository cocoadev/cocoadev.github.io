---
layout: page
title: NSUndoManagerTutorial
---


� Copyright 2001 Raphael Sebbe

As I recently went through implementing undo/redo support in some projects of mine, I decided I would share this experience with the Cocoa community. I will first cover the basics of NSUndoManager, next the higher level considerations of how it is integrated into the application design and finally some practical considerations of implementing undo. I have learned most of this by looking at Apple's Sketch.app, available on every MacOS X development installation, and actually making the implementation of it into applications.

----
**Basics**

Cocoa frameworks offer a really elegant yet powerful way of handling undos and redos in applications. The main idea behind it is that each time you perform an operation, you provide the application the operation that will revert it. Those undo operations are put into a stack and retrieved when the user hits the undo button.

NSUndoManager class is used to build the undo stack, using either of the following methods, 

    
[undoManager registerUndoWithTarget:target selector:@selector(myMethodWithArg:) object:argument];


for methods with one (Objective-C object) argument, or the more general, invocation based method,

    
undoManager prepareWithInvocationTarget:target] myMethodWithArg1:arg1 andArg2:arg2];


A simple example of method implementing undo could be :

    
- (void)setSize:([[NSSize)aSize
{
  undoManager prepareWithInvocationTarget:self] setSize:size];
  size = aSize;
}


so that each time -setSize: is invoked on my object, a -setSize: invocation using the old size is recorded and later invoked when the user chooses "undo" in the Edit menu. Note that all operations resulting from a single event loop (ie, a button click) will all be undone at the same time.

You can also choose the action name, the one that will appear in the menu, using this :

    
[undoManager setActionName:@"Change Size"];


And that's about it ! It is that easy to get it to work. But, if you are interested to know more on how it integrates into aplication design, and to know about some implementation tips, then read on !

----
**Some theory**

First, what you need is a valid [[NSUndoManager object. With document-based Cocoa applications, an undo manager is automatically created for you for each document. This is appropriate for most cases, but there are some where you need more undo managers, for example an IDE whose documents would be projects but would still need undo support at the file level; in that case an undo manager should be created for each file. 

When multiple undo managers are used, they should have a well defined "scope" so that state changes are not partially recorded among those, eventually leading to incoherent undo stack. Implementing undo support in a document-based app offers the additional benefit of automatically managing the document "dirty state", ie, the application knows that the document has been edited and will ask the user to save it before quitting, the document window will shows the "dot" inside the close button...

Second, and this is probably the most difficult aspect of adding undo support, you must decide which operations will be undoable, which won't and make sure that the operations that are not undoable let the application in a state compatible with the recorded undo operations. 

Let's take an example to make that clear. In a drawing software, you can create and delete shapes, change their size and their color. Changing size and color can independently be undoable, as they won't collide : you can always revert to the previous color of one of the shapes even if its size has been altered in the mean time, and vice-versa. But if the shape deletion operation has not been recorded, that means that the color change, that has been recorded just before the object was deleted, won't be applicable because the state of the app is not anymore compatible with the state of when the last operation was added to the undo stack. To summarize this, there is a small set of operations that must be completely undoable to assure the application remains in a state coherent with the undo stack.

*OK for undo's so far, but what about redo's?*

That is really beautifully done in Cocoa. Let's use an example to show how it works.

    
- (void)setColor:(NSColor*)color
{
  [self undoManager] prepareWithInvocationTarget:self] setColor:_color];
  
  [_color release];
  _color = [color copy];
}


When -setColor: is invoked with "red", it pushes a -setColor: invocation into the undo stack with "blue", the old color, as its argument. So, when the user choose undo in the Edit menu, -setColor is invoked with the "blue" as its argument. Here is the nice part : [[NSUndoManager knows it is now undo-ing the last action and it thus now record all undo operation into the redo stack. In this case, the current color, "red", is pushed into the redo stack. Note that it is thus important that a method pushes itself in the undo stack.

----
**Undo and the MVC paradigm**

Application design can be done in various ways. One path often followed by Cocoa developers is the Model-View-Controller paradigm. It makes the distinction between the models, the objects that implement the core data and operations on it, the views, which is the graphical user interface, and the controllers which make the connection between the other two. One question you may ask, and I asked this myself when first toying with undo stuff, is where does the undo considerations fit into the MVC paradigm.

It turns out that most of the undo implementation work should be done at the model level. Models often have a set of primitive operations on which other model and controller operations are based. This means that supporting undo for those will make it available whatever the context in which they are used. Reusing the example of the drawing program, if the model method -[Shape setColor] is undoable, then every action in the application that come back to call this one will be undoable. This means the color well in the inspector, the copy/paste color menu item or the AppleScript command doing exactly that... All those will be undoable without even writing a single line of code specifically for them.

Implementing undo at the model level does also mean that you need a mecanism to let the controllers know that the model has been updated, so that the GUI is correctly updated. As you obviously don't want the model to know its controller, NSNotification can be used for this.

Controllers also have something to say when it comes to undo. A single user action can translate into many model primitive operations which don't know by themselves what it is for. Controllers know because they implement that action (in Cocoa's sense), and they can provide the name that will appear in the undo menu. This could be done like this :

    
- (IBAction)changeParameter:(id)sender
{
  [model1 setFoo:bar]; 
  [model2 setBar:foo];

  self undoManager] setActionName:@"Change Parameter"];
}


----
**Practical considerations**

*Opposite methods*

When a method does exactly the opposite of another, they call each other when recording into the undo stack. 

    
- (void)addChild:(id)object
{
  [[[self undoManager] prepareWithInvocationTarget:self] removeChild:object];
  [children addObject:object];
}

- (void)removeChild:(id)object
{
  [[[self undoManager] prepareWithInvocationTarget:self] addChild:object];
  [children removeObject:object];
}


*Create / delete methods*

Those methods must be undoable as shown above, otherwise the undo stack can be corrupted. That means all objects created must stay over as long as you want to keep undoability. But [[NSUndoManager won't retain those for you and you should set up a pool of saved objects that keeps a reference on the ones that have been deleted. When the user chooses "undo Delete", you re-add it to the document and remove it from the pool.

In the case of objects using a lot of memory resources, you should provide a way to clear the undo stack (and the saved object pool) to the user.

*Interactive (mouse) control and undo*

Let's consider the resizing of a shape in our drawing software. It is done through the Shape's class instance method -setSize:, that pushes the old size into the undo stack. But when the user interactively resizes the shape, hundreds, or even thousands of -setSize: method invocations are made. But you don't want to record all those, as it would use a lot resources (memory) and more, only the last size interests you (when the user releases the mouse button).

What is typically required to handle that particular case is a flag that tells whether you record undo operations or not and a way to tell : "I am finished with the mouse, just record the change now !". Here is a possible solution to this problem, largely inspired from Sketch.app sources.

First, you need that flag, as an instance variable. Lets call it "manipulating"; it is YES when the mouse button is up and FALSE when it is down. Next, you need 2 methods. The former to tell the object that you start manipulating the object's state and you don't want to record undos, and the other one to tell you have finished manipulating and you want to push the change in the undo stack.

"manipulating" and "oldSize" are ivars, -setSize is the undoable primitive method that sets the shape's size. The -startManipulating method, typically invoked on mouse down, basically tells to save the current size (for later use) and not to record any further undo operations (mouse drag events). You can see in -setSize: that undo is not recorded when manipulating is YES.

-stopManipulating is trickier. Its saves the current (new) size in a local variable, sets back the old, saved one then tells the object to record change. Next, it sets the new size, but this time, it is recorded in the undo stack and it is the only one to be so.

    
- (void)startManipulating
{
  manipulating = YES;
  oldSize = size;
}

- (void)stopManipulating
{
  NSSize newSize;

  newSize = size;
  [self setSize:oldSize]
  manipulating = NO;
  [self setSize:newSize]; // this one is recorded in the undo stack
}

- (void)setSize:(NSSize)aSize
{
  if(!manipulating) [self undoManager] prepareWithInvocationTarget:self] setSize:size];
  size = aSize;
  [self notifyChange]; // tell controllers...
}


*Reverse order*

Just remember that the methods pushed in the undo stack are invoked in reverse order on undo. For example, if you want to push some kind of notification method in the undo stack (this is probably not a good application design, but it good to explain this issue ;-) ), push it before pushing the method doing the actual change : 

    
- (void)setSize:([[NSSize)aSize
{
  [self undoManager] prepareWithInvocationTarget:self] notifyChange];
  [[[self undoManager] prepareWithInvocationTarget:self] setSize:size];
  size = aSize;
}


----
**Conclusion**

Implementing undo for Cocoa applications is quite straightforward, but simple rules to preserve the state of the application should be enforced. In particular, all the methods working with the same data, and that could be a source of corruption of the undo stack, should be made undoable at the same time. Next, MVC should be taken into account when implementing undo, as it will make it more widely available to the app. Eventually, there is a number of practical considerations that you should be aware of when adding undo support.

I hope this article will be of interest when you'll need to implement undo in your next killer Cocoa application ! Thanks for reading.

----

A good article! One thing though - you state: "But [[NSUndoManager won't retain those for you". It appears it will, in fact. With invocation-based Undo, it depends on whether the invocations are set to retain their arguments. The documentation for NSUndoManager isn't explicit about this point, but my experience is that objects you pass to it are retained by the undo manager. The same is true of the simpler target + object undo approach. This means that there's no need to set up a pool for handling deleted objects - the undo manager itself retains those objects so they are never really deleted - they are stored in the undo stack. Maybe this is a change to the undo manager's behaviour that came in with a certain system version (???) but if you think about it, it makes sense for the undo manager to retain any objects it is passed. Then no matter what the rest of the app does with them, they are always available for undo - only when the undo task itself is thrown away is the object finally released.  --GrahamCox.

----

You're right. I believe this changed a couple years back (I remember somehow this problem was real at the time). --RaphaelSebbe

