---
layout: page
title: NoviceHelpWithsetFrameAutosaveName
---



I've just started to learn Cocoa programming with Objective-C. I've created the sample Currency Converter application and now I would like to use the setFrameAutosaveName delegate to remember the last position of the window. First of all, can someone give me a simple definition of a delegate. I've read some definitions about it, but still don't really understand it well (I'll give an example further on).

----
A delegate is an object which assumes some responsibility on behalf of another object. Using a delegate lets you implement specialized behavior in an object without having to create a subclass. In many frameworks, for example, if you want something special to happen when a window closes, you create a subclass of the framework's window class. You could do that in Cocoa if you wanted to, but a more common technique is to set up another object as the window's delegate and implement the method -windowWillClose in the delegate. -CS
----

Ok, in MainMenu.nib there are these instances: Window (view), ConverterController (controller), Converter (model). One reference mentioned putting setFrameAutosaveName in the following delegate:
    
- (void)applicationDidFinishLaunching:(NSNotification *)notification

After adding this to my controller (ConverterController.m), it does not receive the message sent when the application is finished launching; it never gets called. To remedy this, I control-dragged a connection from File's Owner to the controller and set the File's Owner's delegate to ConverterController. However, if I add an awakeFromNib method, this always gets called even without setting the delegate. This is part of my confusion.

Q: Why is applicationDidFinishLaunching defined as a delegate and awakeFromNib defined as a method. Is it that the awakeFromNib method is being directly invoked from some other place (i.e. not via a message) while applicationDidFinishLaunching is, on the other hand, listening for the appropriate message? And why do I have to set File's Owner's delegate for this to work?

----
-applicationDidFinishLaunching is a delegate method of NSApplication, which means that any instance of NSApplication will call it's delegate object's -applicationDidFinishLaunching method when the app is up an running. The delegate can use that method to do anything special that this particular application should do when it starts up. Your object has to actually be the application's delegate in order for its -applicationDidFinishLaunching method to get called, though. In your MainMenu.nib file, the File's Owner icon represents the application object, so setting your controller to be File's Owner's delegate makes your controller the application's delegate. -awakeFromNib, on the other hand, isn't a delegate method at all. When a nib gets loaded, every object that's part of that nib gets sent the -awakeFromNib message. -CS
----

Getting back to setFrameAutosaveName, I placed this in applicationDidFinishLaunching. However, most references that I've seen on this call setFrameAutosaveName like this:
    
[self setFrameAutosaveName:@"WindowPosition"];

or
    
self window] setFrameAutosaveName:@"[[WindowPosition"];

This does not work for me, because in the first case, [self] would refer to the controller, which is not an NSWindow and this does not have setFrameAutosaveName while in the second case, [self window] implies that the controller contains an object named window. So, it is obvious that other programmers are invoking setFrameAutosaveName from other locations.

What I did instead was to use one of the outlets to get a reference to the window:
    
rateField window] setFrameAutosaveName:@"[[WindowPosition"];

This works, but I'm not sure that this is the "right" way to do it.

Now, even though this works, here's what happens when I launch the application. For a brief moment, the main window opens at the hard coded location defined by the position of the window during development in Interface Builder. Then it moves to the saved position. Since this doesn't happen with other applications I use, I must be missing something or doing something wrong.

Q: How should something like this be implemented?

----
The easy way to do this is to set it up in the nib file. In Interface Builder, open the inspector for your window and enter the name that you want to use for the saved position in the Auto Save Name field.

If you really want to set the autosaved frame name in code, you should make your window initially hidden (uncheck the 'Visible at Launch Time' checkbox in the inspector for the window in IB). Also, you might consider moving your code to your controller's -awakeFromNib method. The effect will be the same, but -awakeFromNib is a better place to set up the window. -CS

----
"CS" : This is by far the easiest way and works perfectly. Thanks!

----
The reason that you usually use     [self window] is because you usually set up an outlet to any objects you will be using, including windows (it's "better style" than using another one of your outlets, but then again it does take one more pointer). Also, the typical File's Owner classes NSDocument and NSWindowController both have     window outlets by default, though apparently NSDocument does not contain a corresponding     -window method. Of course, for custom controller objects, the name "window" is arbitrary, if common. --JediKnil

