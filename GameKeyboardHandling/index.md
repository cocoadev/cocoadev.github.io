---
layout: page
title: GameKeyboardHandling
---

I propose here what I think is a simpler solution than that proposed in GameKeyboardHandlingAlmost.

Game Keyboard Handling Solution
----

When writing games or many other programs that heavily involve keyboard interaction it is often desirable to respond many keys being held at once. A common example of this is the user's desire to perform an action while moving or to move and turn or move diagonally. This was the inspiration for the solution I present below. Note, however, that this solution can be used in any program where the user would likely be holding keys down while pressing others. Through the use of Cocoa data structures like NSMutableSet this solution can be trivially extended to support any number of simultaneous keys. If you wanted to you could use it to support even the absurd edge case of all keys on the keyboard being held at once.

Onward to the solution. There are three steps to this solution: configuring your NIB file in InterfaceBuilder, modifying the header of your custom view class, and finally adding the code for multiple-keys pressed support to the implementation file. I sincerely hope this solution proves as effective for you as it has for me.

STEP 1: Configuring your NIB file in InterfaceBuilder
----
In your MainMenu.nib or MyDocument.nib file (or any other nib where you have custom drawing code that needs keyboard input) make a connection from the Window object in the Instances tab to the custom view in the window itself and connect it to the     initialFirstResponder outlet. If you don't know how to do this, I recommend doing the CurrencyConverter tutorial in Apple's Developer Documentation first.

STEP 2: Modifying the Header of Your Custom View Class
----
Header file:
    
@interface MyCustomView : NSView
{
  NSMutableSet * keysPressed;
}
- (void)processKeys;
@end


STEP 3: Adding Multiple-keys Pressed Support
----
The rest of the code shown is within the @implementation M<nowiki/>yCustomView and @end delimeters in M<nowiki/>yCustomView.h.

Declare that your view will accept keyboard focus:
    
- (BOOL)acceptsFirstResponder {
  return YES;
}


Add keys of interest to the keysPressed set as they are hit:
    
- (void)keyDown:(NSEvent *) theEvent {
  if (!keysPressed) {
    keysPressed = [[NSMutableSet alloc] init];
  }
  NSNumber * keyHit = [NSNumber numberWithUnsignedInt:
    theEvent characters] characterAtIndex:0;
  switch ([keyHit unsignedIntValue]) {
    case NSUpArrowFunctionKey:
    case NSDownArrowFunctionKey:
    case NSRightArrowFunctionKey:
    case NSLeftArrowFunctionKey:
    case ' ':
      [keysPressed addObject:keyHit];
      break;
    default:
      break;
  }
}


Remove keys of interest from the keysPressed set as they are released:
    
- (void)keyUp:(NSEvent *)theEvent {
  if (!keysPressed) {
    keysPressed = [[NSMutableSet alloc] init];
  }
  NSNumber * keyReleased = [NSNumber numberWithUnsignedInt:
    theEvent characters] characterAtIndex:0;
  switch ([keyReleased unsignedIntValue]) {
    case NSUpArrowFunctionKey:
    case NSDownArrowFunctionKey:
    case NSRightArrowFunctionKey:
    case NSLeftArrowFunctionKey:
    case ' ':
      [keysPressed removeObject:keyReleased];
      break;
    default:
      break;
  }
}


Setup up a periodic key processing operation:
    
- (id)initWithFrame:(NSRect)frame {
  self = [super initWithFrame:frame];
  if (self) {
    [NSTimer scheduledTimerWithTimeInterval:0.03
                                     target:self
                                   selector:@selector(processKeys)
                                   userInfo:nil repeats:YES];
  }
  return self;
}

- (void)processKeys {
  if ([keysPressed count] != 0) {
    NSEnumerator * enumerator = [keysPressed objectEnumerator];
    NSNumber * keyHit;
    /* process all keys of interest that are held down */
    while (keyHit = [enumerator nextObject]) {
      switch ([keyHit unsignedIntValue]) {
        case NSUpArrowFunctionKey:
          /* Your Up Arrow handling code */
          break;
        case NSDownArrowFunctionKey:
          /* Your Down Arrow handling code */
          break;
        case NSRightArrowFunctionKey:
          /* Your Right Arrow handling code */
          break;
        case NSLeftArrowFunctionKey:
          /* Your Left Arrow handling code */
          break;
        case ' ':
          /* Your Space Bar handling code */
          break;
      }
    }
    /* ask for a redraw at the next opportune time */
    [self setNeedsDisplay:YES];
  }
}


As a result, the compound effect of multiple keys that are pressed is applied to each frame. Simply add more     cases to each switch statement to support more keys or put the add and remove code in the     default block to support the full keyboard.  How's that for under 75 lines of code?

If you want to enhance this page please add descriptive comments to the code itself or post comments, additions, and feedback below. This isn't a perfect solution, but it's easy to implement and separates key capturing from processing.

--AlainODea

----

That's wonderful for simple keyboard handling, but it needs some work for more complex keyboard use.

Specifically, this doesn't cover state-dependent processing of keys or the order of key processing.  I do like your use of a mutable set and key detection, however, that would work well paired with a more capable key processor.

I've got a rough idea of how this might be designed.  We will need:


*CCDKeyEventDispatcher - Handles the dispatching of a single or multiple key events in the same way.  This simply contains the keys we want to respond to and a way to dispatch the event.  You could make this an abstract class, with subclasses that dispatch via selector or notification.  This is essentially a slightly modified version of the Command DesignPattern.  

*CCDKeyProcessor - Our processor (could be a singleton), based on the State DesignPattern.  This will hold the set of keys down (key events notify or call the key processor rather than manipulating the set directly).  It will also set up the timer and process the keys down at the appropriate interval.  The processor will also keep a dictionary of mutable collections of CCDKeyEventDispatchers, keyed by a user-defined string.  



So, here's how it works:



* The programmer makes a new mutable collection, and adds or inserts new CCDKeyEventDispatchers, essentially binding keys to a dispatching mode (selector on an object, notification, whatever).  **This collection represents a state which only responds to the keys associated with the key dispatchers it contains.**

* The collection can be a set if the programmer doesn't care about the order in which keys are processed.  If order matters, an ordered collection, like a mutable array, should be used.  In fact, if there are dispatchers which will modify the state, it's best to use a mutable array and keep state-modifying dispatchers at the front.

* As they're completed, the collections are passed to the CCDKeyProcessor along with a string state name to act as a key.  The key processor will throw this into its dictionary of dispatcher collections.  

* Once all dispatcher states have been set, the key processor is started with the name of the first state to use.  The key processor starts the callback (or, alternatively, you can start when the first keyDown event is received).  

* When there are keys down, the key processor creates a temporary mutable set for keys it has already processed.  This is necessary to handle state changes (I'll get to that later).  It then iterates through the current dispatcher collection.  

* For each key dispatcher in the collection, for each key handled by the dispatcher, if the state hasn't changed and if the key associated with the dispatcher is not in the processed key set, but is in the set of keys down, the key is added to the processed key set and an event is dispatched.  

* Responses to certain events may include changing the state.  If this occurs mid-processing, a flag is set in the key processor.  When the flag is detected, the key processor stops its iteration and begins processing with the new state.  We can ignore keys which have already been processed since we've been keeping track of them in a temporary set.

* When all dispatches have finished, the changedState flag is reset and the processed keys set is cleared.  We then wait for the next callback.



So, what do you think?  Is this too complex, not complex enough, or just right?

-- AndrewBowman

----

Andrew, that's a very good point about my solution losing the key ordering and thus losing an important aspect of key state. From my brief review of your suggestion it seems to be quite sophisticated. My goal in mashing-up the solution I did was to enable people to support very simple keyboard support for games needing to supporting many keys down at once (up and left arrows for example) assuming a one to one relationship of keys to actions. What you are describing sounds like a key dispatcher for keyboard shortcuts in a windowed UI. More than what I need, but perhaps essential in more sophisticated scenarios.

--AlainODea

