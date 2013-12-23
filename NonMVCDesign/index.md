---
layout: page
title: NonMVCDesign
---

Hia, 

Just an informal poll. How many people strictly stick to MVC? I am in the midst (near end) of a large Cocoa/C++ project which has C++ as the model layer, and Cocoa as the view and controller layers. There were a few design dilemmas (as in any large project), and I found that many of them were simply there because of my over-strict adherence to MVC. For example, my app uses a lot of AudioUnit<nowiki/>s, (wrapped in a AudioUnit Obj-C class), and I was trying to figure out how to make a MVC compliant view system for the AU's. One important caveat was that each single AudioUnit instance was to have 1 and ONLY 1 editor window. Having more would not harm the system, but they would waste resources for zero added value. So I was thinking of a way in which I could associate the editors somehow with the units, to enforce the 1 editor per unit policy. I came up with all sorts of ways, including having a singleton ensure a 1 to 1 relationship, but each one suffered from one subtle problem or another. Finally I realised that the best way would be to screw MVC (this time) and embed the editor in the AU class:
    
- (void) showEditor
{
    if(!editor)
        editor = [[AUEditorController alloc] initWithWindowNibNamed:@"AUEditor"];

    [editor showWindow:self];
}

This worked perfectly. There is no (one extra pointer :P ) overhead for users who never load an editor, and there is never more than one editor per unit instance. I ended up exporting this pattern throughout the application and found there to be a couple of consequences:
- All the view code got simpler
- All the view code became more brittle
- The multiple view/single data pattern is inapplicable.

The pro's outweighed the cons in this case (as the view code was the most complicated in the app, and it was drastically simplified), so I stuck with it. I know that MVC is tried and true, but like any technology it is not universally applicable. So my question to you is where have you 'broken the rules' and used an alternative?

Jeremy Jurksztowicz

----

Do you mean you stored a link to a (unique) controller object in each model object? You could alternatively have kept a dictionary mapping each model object to its controller object (and one vice-versa). Did you consider this approach?

----

I haven't got a good mental picture of your architecture, unfortunately, but how about having a controller that pools the audio units? When a new editor is created (by the controller, presumably) the controller checks if there's an audio unit in the pool, connects it with the editor, and removes it from the pool. When the editor is closed the audio unit is pooled again. If there is no audio units in the pool, a new ones are created as needed. This way you're reusing the audio units (which seem to be a scarce supply) and you won't create new ones unless it's absolutely needed.

Coupling components, like the view and the model is usually a bad idea, as tight coupling always is, but you seem to know that already. At the same time, it's not necessarily a bad idea to include the view in the model directly, since the model object knows better than any one else about what controls and fields the view should present (see the article linked from GettersAndSettersAreEvil for more info on that).

--TheoHultberg/Iconara

----

Not surprisingly, the best software is software that works. Working software that includes design decisions you aren't sure about - or are flat out unhappy with - is much better than non-working software while you stare at your navel looking for the most 'perfect' design decision.

I've had more than one occasion where I couldn't find the best MVC-compliant design after a day of wrestling with it - so I put something in place that worked, and moved on. Usually I have an 'AHA' moment a week later. Whether or not I implement the 'AHA' idea depends on how much time I have or how important it is to me.

Honestly, every serious flaw I've had when maintaining long-term code has been around code that isn't MVC. Obviously my preference then is to spend *some* extra time looking for an MVC solution. How much depends on deadlines, interest, and frustration. At the end of the day, though, software isn't useful if it isn't used. It can't be used if it isn't finished, and the users don't care a flyin' flip about artsy-fartsy artistic design decisions. They want the computer to help them do their work.

So - in your case, you tried hard to find an MVC solution, and finally ended up with a solution to the problem that isn't MVC. Don't sweat it. If you have any misgivings, put a //REDTAG comment in your code and move on. My experience tells me that your solution will be the source of a limitation or trouble later on. Still, it's better to continue to solve the larger problem now, and come back to this issue later on. Maybe there won't be any issues. Maybe in a week you'll have your own 'AHA' moment. And maybe... maybe.... The list goes on forever. You solved the problem with your available toolset to the best of your ability. Good enough.

So yeah - it's frustrating when a bad design choice is implemented, but in the bigger picture, it's not nearly as frustrating as not delivering code on time. I make better MVC code now than I did a year ago - and in a year I'll do better than today.

--TimHart

----

"Cocoa Programming" Chapter 26, "Application Requirements, Design and Documentation" shows one interesting solution to your problem.  The solution is to extend the model class using a category whose implementation is actually in the controller or view layer.

In your case, 

    

@implementation AudioUnit (EditorSupport)

- (void) showEditor
{
    static NSMutableDictionary   *controllerDict = nil;

    if(nil == controllerDict)
    {
       controllerDict = [[NSMutableDictionary alloc] init];
    }
   
    AUEditorController *editor = [controllerDict objectForKey:self];
    if(!editor)
    {
        editor = [[AUEditorController alloc] initWithWindowNibNamed:@"AUEditor"]; 
        [controllerDict setObject:editor forKey:self]; 
    }

    [editor showWindow:self];
}

@end



The advantages:
   - No instance variable is added to the model class just to support editing.
   - Any object in the controller or view layers may use the -showEditor method, and object in the model need not know it exists.
   - The code is not particularly brittle: the same technique (using a different category and a different method) could add other editor types so that in no coupling from the model to a particular controller or view.
   - The controller/view specific code is implemented in the controller/view layers and is not in the model at all.  For example, if the model is implemented as a framework, the EditorSupport category method is not in the model framework.

For another example of this technique: See the way the ApplicationKit framework adds view related methods to the NSString class declared and implemented in the Foundation framework.

*I would say the fact that it doesn't add an instance variable is actually a con � it reimplements the instance variable system using an NSDictionary just so it can say it doesn't use an instance variable. D'oh!*

I think, its a trade off. One thing to remember is that dictionaries retain objects, so In order to avoid circular references, some extra care is needed.
    
@implementation AudioUnit(EditorSupport)
- (void) setUIElement:(NSView*)view
{
    [additions setObject:[NSValue valueWithPointer:(void*)view] forKey:@"MyUIKey];
}

- (NSView*) UIElement
{
    id obj = [additions objectForKey:@"MyUIKey];
    if(obj) return (NSView*)[obj pointerValue];

    return nil;
}
@end


Sorry, the example is a little contrived :)

----

Better still, just NSMapTable with NSNonRetainedObjectMapKeyCallBacks and NSNonOwnedPointerMapValueCallBacks and avoid retain cycles, NSValue instance creation, etc.

*I would say the fact that it doesn't add an instance variable is actually a great plus.  I don't think the model object should contain any view specific information.  By not containing information about a particular view within the model, the design is left open to supporting any number of different views of the same model.  By not storing view information in the model, model objects can be easily archived and unarchived, put in undo stacks, copied, etc. which is much more complicated and requires special case code (within the model) to support if view information is mixed into the model*

**Adding a new instance *method* to the Model classes to support a particular View system is almost as bad, though, from a conceptual standpoint. Much better is simply to keep a dictionary mapping model object to view object in one of your controller classes! You don't get retain cycles this way, either, since neither Model nor View will ever retain a Controller object.**

Adding a new instance method via a category in another subsystem of the application is not bad at all.  Methods declared and implemented in the view subsystem are maintained and used within the view subsystem.  Methods declared in the model subsystem are maintained and used within the model subsystem.  Modularity and encapsulation are preserved, future flexibility is preserved, the code is not brittle, and the implicit association between instances of a model object and the editor(s) for that model object is provided in the most convenient way.  e.g. a view object can just ask the model object for its editor.

**Agreed, however there is a point to be made about sloppy category usage. If the view support category is simply included everywhere with the model class then it is a s good as adding a convenience method to the class. Careful separation (logical and source code) can keep the category from 'melding into' the class and being abused.**

*Just to get some perspective, the two alternatives boil down to     [model myView] and     [viewsForModels objectForKey:model]. The disadvantage of using the category approach, however, is being forced to store the model-view association in global memory, not per controller object. What happens if you have two documents open at once? They are arbitrarily forced to use the same association dictionary. Any code which forgot that (say, using the model-view association dictionary to find all open views associated with a model object) will break.*

