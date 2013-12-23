---
layout: page
title: LoadingWindowlessNIB
---

Oct. 28, 2004

I'm trying to implement something like what the NSColorPanel does when it allows an external NIB (with code) for a color picker to be added to the main color panel window (via **provideNewView:(BOOL)initialRequest**). What I'm trying to plug into my app is an NSView subclass containing some buttons and other stuff which is bundled with code for responding to the UI events. I can build this without errors and I can load it (via **NSBundle loadNibNamed:@"nibName" owner:self**) but when I then try making it a subview of an existing view in the app's window it gets the following error: 
    
*** +[T**'estTool window]: selector not recognized.

I've tried more ways to get around this than I can remember ... At one point I could get the plugin view added as a subview but it was empty (sending it a *subviews* message returned an empty array). 

I keep thinking I'm in the general vicinity of the "right way to do this" but am just missing something not obvious to me, but hopefully obvious to you. Does anyone have a working example of this kind of thing I can look at? Thanks in advance.

----

What does your code look like? What's the stack trace when that "selector not recognized" error happens?

----

What's the FilesOwner of your nib?

----
Log from run:

    
[snipped] ...


Code From Tool Plugin (T**'estTool):
    
[snipped] ...


----

You are casting the NXConstantString "T<nowiki/>oolsProtocol" to Protocol. You can't do that, and thus theTool will never be set to anything meaningful. What you mean to do is     [toolClass conformsToProtocol:@protocol(T<nowiki/>oolsProtocol)].

*Yeah, I thought that looked a little  funky... I changed it per your suggestion but it made no difference.*

----

The line where it fails reads     [toolsContainingView addSubview:theTool]. On this line,     theTool is a class.     -addSubview: takes an NSView as its parameter. I do not understand why you expect this to work, or what you expect it to do.

Also, you send two init messages to     theNib. You must send one and exactly one init message to every object that you alloc.

*
    theTool is a subclass of NSView and     loadNibForFileOwner returns the     TestTool instance (i.e., self) and     TestTool is a subclass of NSView. I'm not sure where you're seeing a double init for the nib. The initWith and instantiateNib calls?
My understanding was that the     initWithNibNamed:bundle method loads the nib into memory but doesn't init/instatntiate it.     instantiateNibWithOwner:topLevelObjects:  initializes and instantiates it from memory. In the log you see ony one     initWithFrame message, just preceding the     instantiateNibWithOwner:topLevelObjects: call.
*

*No*, self is *not* an instance, it is the *class*. You can see this clearly from your log:
    
    new tool's object description: TestTool

If it were an instance, the description would look something like     <TestTool 0xdeadbeef>.     -loadNibForFileOwner:inBundle: is a *class* method, so self refers to the class.

As far as the double init messages go, the problem is here:
    
  NSNib *theNib = [[NSNib alloc] init]; // one init
  theNib = [theNib initWithNibNamed:@"TestTool" bundle:theBundle]; // two inits

This probably works, but it could break in the future and you shouldn't do it.

*
Like I said at the beginning, it would probably be something obvious. It being a class instead of an instance would certainly explain a few things. I've been messing with this code so much now (what I posted above is **way** cleaner than what I'm actually working with) that I may as well kill it and start clean. I'll make sure the new code is dealing with an instance of the class and most likely that will resolve the problems I've been having with it. Thanks.
*

----
Closer, but still not there ...
    
[snipped] ...


So..., I've managed to get T**'estTool instantiated and its instance (theTool) added as a subview of the main window's "tool area." But theTool has lost all of its components (even though when I open the nib from within the built package IB shows all of the components there). Any suggestions?

----

You never do anything to add a subview to     theTool, so why would it have any subviews?

*Because the subviews were added to it in IB and are there in the nib.*

No.     theTool is the file's owner. File's owner can't have subviews. Whatever you added subviews to is a totally separate object.

----

I've managed to work around the problem using the following method:

    
  NSNib *nib = [[NSNib alloc] initWithNibNamed:nibName bundle:plugInBundle];
  
  Tool *theTool = principalClass alloc] init];
  [nib instantiateNibWithOwner:theTool topLevelObjects:nil];
  
  **theTool = [theTool toolView];**
  
  [installedPlugIns setObject:theTool forKey:toolClassKey];
  
  [toolsContainingView addSubview:theTool];


The     [theTool toolView] method returns the superview for one of the GUI components in the view I'm loading. This ends up being a different instance than when     theTool is instantiated two lines earlier. My assumption, then, is that this first instantiation is the File's Owner. This workaround seemed to be doing the job until I was trying to implement drag & drop for a component in the tool. The line:

    
  [[NSPoint p = [toolWidget convertPoint:[theEvent locationInWindow] fromView:nil];


gets a **signal 11 (SIGSEGV)** and when I log the event it shows:

    
NSEvent: type=LMouseDragged loc=(499,133) time=224186.8 flags=0x100 
win=0 winNum=27146 ctxt=0x15cff evNum=13967 click=1 
buttonNumber=0 pressure=1 deltaX=20.000000 deltaY=0.000000


My guess is that this is related to the fact that I've got 2 instances of theTool out there.

I can't figure out how to get     theTool instantiated (so I have a pointer to it) *and* also get the nib loaded without ending up with two versions of theTool view (the first, empty and the second with the actual tool components). All the docs I've found for doing lazy loading from a nib are for loading windows or panels, not NSView subclasses that have to get integrated into an existing window, so I haven't found anything that I can refer to that will help me understand what's going on and how to properly set this up. Suggestions? Links to relevant docs? Again, thanks in advance.

----

I suggest nuke and pave; your design is incredibly bizarre and this is causing you no end of problems.

Use MVC. Your File's Owner object is a controller. You seem to want the controller and the view to be the same object; don't do this. Redo your API. Have the controller load its own nib, instead of having that nib be loaded for it. The controller should not also be a view. Look at how a regular non-plugin nib works; you have a controller and you have a bunch of views. Make your plugin nibs work the same way.

----

**Nov. 1, 2004**

I've found 2 references for using external nibs (as plugins): BundlesAndPlugIns and TableViewMatrix. I've cleaned up the "bizarre design" making it MVC (actually, just VC as there's really no data involved with what I'm doing at this point) and attempted to use the same techniques found in these 2 references.

**Background:**

"All" I want is to be able to load external plugins into a "tool area" (an NSView subclass) in my main app window and have them function per whatever code they contain. What I don't want, aside from ensuring a plugin conforms to a protocol, is to have code in my main app that is plugin specific (i.e., any code customized for some particular plugin). I want the plugins to take care of drawing whatever they need to and to handle their own events. As the final app will have 30+ tools, I'm attempting to set things up so that I can use a "lazy load" method for them (and it will make adding or updating tools *far* easier).


I'm testing with a fairly simple plugin tool. The tool nib is in its own "plugin" bundle outside of the main nib. The tool doesn't have a window (or panel), just a "containing view" with the actual UI widgets in inside it.

The tool nib I'm testing with has a (default) File's Owner (    NSObject), First Responder (not used),
and 2     NSView subclasses (    ToolView and     BlendView).     ToolView
is the "containing view" and contains 2 color wells and a     BlendView. 

The     ToolView code is the nib's principal class, handling the *initWithFrame* message
as described, below.

The     BlendView code handles drawing a blend (gradient) of the colors in the color 
wells and dragging of colors out of the gradient. It has the necessary     IBOutlet 
and     IBAction connections for this.

Following is the general flow of activity:

    
Main Program                                  Tool
------------                                  ----
Searches for plugins

For any it finds, verifies the principal
class conforms to the "tool protocol"
  If so, saves the bundle info in a 
  dictionary.

...

UI triggers request for tool.

Finds correct bundle, gets the principal      
class for it and does an alloc/init.          
                                              initWithFrame message received.
                                              Checks to see if the nib has been
                                              loaded. If not, does a
                                              **[[NSNib alloc] initWithNibNamed: ...**
                                              and then a **instantiateNibWithOwner:self**
                                              saving pointers to the top level objects
                                              in an array.
Adds the instance as a subview of the                                              
"tool display view."


Here's what a typical log looks like:
    
 1.  TestApp[13023] principal class is ToolView
 2.  TestApp[13023] plugIns after build <CFDictionary 0x3832d0 [0xa01900e0]>{type = mutable, count = 1, capacity = 4, pairs = (
                    3 : <CFString 0x384230 [0xa01900e0]>{contents = "010"} = 
                      <CFArray 0x3843d0 [0xa01900e0]>{type = immutable, count = 4, values = (
                      0 : NSBundle <[...]build/ToolView.plugin> (loaded)
                      1 : ToolView
	              2 : ColorBlender
                      3 : Color Blender)} )}
 3.  TestApp[13023] [toolsController setUpDesignTools] returned YES
 4.  TestApp[13023] Selected subcategory is: Blend (segment 1)
 5.  TestApp[13023] *The tool has loaded its nib in initWithFrame. nib: <NSNib: 0x39d120> ...*
 6.  TestApp[13023] *... top level objects for intantiated nib are: <CFArray 0x39cd60 [0xa01900e0]>{type = mutable-small, count = 2, values = (*
                      *0 : <BlendView: 0x39fe10>*
                      *1 : <ToolView: 0x3a0010> )}*
 7.  TestApp[13023] *... subviews of this class (self: <ToolView: 0x353c70>) are: *
                    *  <CFArray 0x39cd70 [0xa01900e0]>{type = immutable, count = 0, values = ()}*
 8.  TestApp[13023] *ToolView init message processed. self = <ToolView: 0x353c70>*
 9.  TestApp[13023] loaded class <ToolView: 0x353c70> as NSView
10.  TestApp[13023] theTool has subviews: <CFArray 0x39f570 [0xa01900e0]>{type = immutable, count = 0, values = ()}
11.  TestApp[13023] theTool: <ToolView: 0x353c70>, added to containing view: <NSView: 0x3688a0>


Lines 1-3 are written during the "plugin discovery" phase.

Line 4 is the main program getting a tool display request from the GUI.

Lines 5-8 are from the tool's principal class being initialized.

Lines 9-11 are back in the main program.

The net result of all this is that nothing show up, which makes sense as the instance of the tool view has no subviews in it. In line 6, the top level objects for the nib include a     ToolView instance but this isn't the same as what the initWithFrame instantiates (line 7). However, if I fake the main program into "thinking" the     ToolView instance from line 6 is actually the instantiated view, it will show up. But then, it's subviews include a     BlendView different from what is listed (on line 6) ... and if I "fix" this, everything looks okay, and the color blend works, but if I try to drag a color out of the blend it, the first line of the code in the **mouseDragged** method, 
    
  NSPoint p = [toolWidget convertPoint:[theEvent locationInWindow] fromView:nil];

gets a **signal 11 (SIGSEGV).**

What now? 
----

I suggest you read the Plugged In Cocoa tutorial from [http://www.brockerhoff.net/pap.html], if you haven't.

*Thanks, whoever you are... Rainer's paper has the information I was looking for and contains enough conceptual detail for me to finally* get *how this is all supposed to work.*

