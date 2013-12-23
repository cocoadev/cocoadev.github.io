---
layout: page
title: ComponentDevelopment
---




Here�s a tutorial on developing standalone components with Xcode. If you consider this topic just another take on unit testing, so be it, but the primary purpose of this writing is to give a walk through on how to setup a new target for component testing and debugging. 

Xcode is an awesome development environment. The most striking new feature is FixAndContinue. With this advanced debugging technology, developers are now given the ability to modify their code dynamically. FixAndContinue shortens the time it takes to locate/fix a bug and rebuild/run your source. The turnaround times are fast enough to allow coders to seriously reconsider how they approach Mac OS X application development.

I�m not advocating the merits of trial and error coding. Good design should always be a top priority, but FixAndContinue adds a new dimension to product development. Apple�s first public demonstration of FixAndContinue involved user interface debugging. There is a QT movie at ADC that shows XCode in action. I highly recommend watching it. 

This tutorial will expand on Apple�s demo and give a step-by-step guide on setting up a new target application in an EXISTING project. The new target will demonstrate how to utilize FixAndContinue for the development, testing and debugging of an interface component.


*Open an EXISTING Cocoa project in Xcode 
*From the Project Menu select �New Target�
*In the �New Target� dialog, select Cocoa -> Application
*Name your new target �CustomInterfaceElementTest�
*Select the new target from the pulldown menu in the toolbar (see figure below)
*In the Group & Files list expand the �Target� group file:///Developer/Documentation/DeveloperTools/Conceptual/XcodeQuickTour/art/xc_project.gif
*After expanding the �Target� group you should see a group for �CustomInterfaceElementTest� , select this group (make sure this new target group/folder is highlighted before continuing to the next step).
*From the Project Menu select �Get Info�  (make sure the info dialog has, Target �CustomInterfaceElementTest�  Info, for its title)
*Click on the Properties tab, enter �NSApplication� for Principle Class and then enter �MainNIBForCustomInterfaceElementTest�  for Main Nib File 
*Create a new custom view  (File -> New File - > Objective C � NSView subview)
*Name this new custom view �CustomViewForCustomInterface� and add this class to your new target (you will be presented with a dialog to select the targets for this class after you have named it, so don�t forget to add this class to your target here)
*Add the class to the new target if you forgot to do it in the previous step (Project -> Add Files�)
*Add the Cocoa Framework to your target (Project -> Add Frameworks�)
*Add the main.m file to your target. You should already have a generic main.m file created for the Cocoa Application Target that was part of the initial project, but if you don�t have one, create a new empty file (File -> New -> Empty File in Project) for your project named �main.m�. Add this file to the new target (Project -> Add Files�) and update �main.m� with the source below:

    
#import <Cocoa/Cocoa.h>

int main(int argc, const char *argv[])
{
    return NSApplicationMain(argc, argv);
}



*Launch Interface Builder
*If the �Starting Point� dialog is not active, open this dialog (File -> New)
*In the �Starting Point� dialog, select Cocoa -> Application
*Click on the new nib window (nib windows have a tab view with tabs named Instances, Classes, Images, Sounds and Nib)
*Save this nib into your project folder (File -> Save) and name this nib �MainNIBForCustomInterfaceElementTest�, but don�t hit return until you read the next step
*After you successfully enter the name for the new nib, you will be asked which target you would like to add the nib to. Uncheck everything and then check the target named �CustomInterfaceElementTest�
*In the nib window select the Instances tab and then double click on the window icon. 
*Drag a Custom View onto the main window.
*Drag an NSButton onto the main window and change the title to �Go�
*In the nib window select the Classes tab and select NSObject
*With NSObject selected, subclass NSObject (Classes -> Subclass NSObject)
*You should see a new subclass named MyObject, change the name to �CustomInterfaceController� 
*With CustomInterfaceController selected, instantiate this new class (Classes -> Instantiate CustomInterfaceController)
*In the nib window reselect the Instances tab and then double click on the instance for CustomInterfaceController (you should see an info dialog pop up for this instance, if you don�t repeat this step)
*Add an outlet named �customView�
*Add an action named �go:�
*Connect the Custom View in the main window to the outlet �customView� that you added to the instance for CustomInterfaceController (if you don�t know how to make connections check out the section on �making connection� at the link below)

file:///Developer/Documentation/DeveloperTools/Conceptual/IBTips/index.html

*Connect the NSButton in the main window to the action �go:� that you added to the instance for CustomInterfaceController
*In the nib window reselect the Classes tab and then select the new class �CustomInterfaceController�
*Save the current state of the nib file (File -> Save)
*Select the new class in the nib window (click on the Classes tab and then select the NSObject subclass you named �CustomInterfaceController �). Create files for the class (Classes -> Create Files for CustomInterfaceController) making sure you insert the files into the target �CustomInterfaceElementTest�
*Go back to Xcode
*Drag either the header or source file for the custom view �CustomViewForCustomInterface� onto the nib window for �MainNIBForCustomInterfaceElementTest� in Interface Builder (this action can also be accomplished by selecting the Classes tab in the nib window in IB and then parsing the header file �CustomViewForCustomInterface.h� by selecting the menu item Classes -> Read Files� and then selecting the header file �CustomViewForCustomInterface.h� for parsing)
*In Interface Builder select the Custom View in the main window
*With the Custom View selected, edit the custom class for the CustomView by selecting the menu item Tools -> Show Info -> Custom Class (from the popup menu in the info dialog) -> CustomViewForCustomInterface (from the Custom Class list)
*Save the changes (Files -> Save)


Hopefully I didn�t miss anything. If everything went smoothly, you should have a working skeleton for an NSView subclass test setup. Now the fun begins. 


*Open the debugging window in Xcode (Debug -> Show Debugger)
*Drag the �CustomViewForCustomInterface .h� file icon from the Groups & Files list in the main project window onto the editor in the debugging window
*Drag the �CustomInterfaceController.h� file icon from the Groups & Files list in the main project window onto the editor in the debugging window. 
*Click on the �Build and Debug� icon in the top left corner of the debugging window. 
*If everything was done properly your new application target should build and run in debugging mode


So what�s the big deal about this setup? Two things were accomplished by following these steps. 

The first thing to take note is the fact that you now have a separate application target working in the same project as another Cocoa application. This setup is useful when unit testing code. You now have the knowledge to create separate targets with unique nib files for testing individual interface objects. Isolating an interface element in a dedicated target encourages the creation of reusable code. Sounds like a trivial thing, but reinventing the wheel over and over again is not a trivial practice.

The second thing to take note is the fact that you have just started the process of creating source code with a running application, but the custom view does nothing�. yet. 

The running application is now supported with the facilities to modify, rebuild and continue interaction without terminating the execution of the initial launch. The tape dispenser icon in the toolbar is key to the process of modifying your code while your code is running. 

To demonstrate this, open the �CustomViewForCustomInterface.m� file in the debugging editor. You should be able to select this file from the pulldown menu located on the left side between the toolbar and the editor. If this file is not available, repeat the step where you drag the file icon from the main project window onto the debugging editor in the debugging window.  Once you have �CustomViewForCustomInterface.m� open in the editor, add the following code to the �drawRect method:

    
- (void)drawRect:(NSRect)rect {
    
    NSRect visibleRect = [self visibleRect];
    [[NSColor greenColor] set];
    NSRectFill(visibleRect);
}
 

Now modify the go: action method in �CustomInterfaceController.m� 

    
- (IBAction)go:(id)sender
{
    [customView setNeedsDisplay:YES];
}
 


Once you have made these changes click on the Fix icon and then click on the �Go� button. The background to your custom view should now be green. 

Change the color to blue and repeat the cycle. 

Some things I have noticed while working with FixAndContinue:


*You cannot add methods while your application is running in debugging mode
*You cannot log errors, but since you are in debugging mode this should be a non issue since you can add breakpoints on the fly


--zootbobbalu

----

Wow, that's a terrific writeup. Perhaps you should consider sending it to a tutorial site like cocoadevcentral.com for publication. The unit testing aspect sounds interesting. Could you expound on that?

-EricWang

----

What's wrong with publishing it on www.cocoadev.com? 

-- MikeTrent

----

I agree with Mike. This site is worthy of publication. I have gained more useful Cocoa knowledge here than anywhere else (including books, except maybe Cocoa Programming by ABY).

To answer you question about unit testing:

I really feel that this topic isn�t the proper place to talk about a subject that deserves more attention. I�m using the term unit testing loosely. Check out the links below if the term is foreign to you. Notice that the topic posted here (UnitTesting) mentions that unit testing is difficult to apply to UI elements. I�m not trying to tackle this issue here, but I do feel my article does lay the foundation for the possibility to incorporate a flexible framework to test interface components properly. The lightweight target �CustomInterfaceElementTest� is a nice support structure for testing single UI elements independently. Working with an isolated NSView subclass forces you think smart (yes I do believe it�s possible to think dumb). Using a lightweight target within a project to develop an NSView subclass allows you to keep your source local and easily accessible to multiple targets. FixAndContinue only adds to this equation (I�m sure the coolness factor will wear off, but it�s still a useful feature for getting the graphics right).

I feel that�


*A good UI element should be able to function without a bunch of connections to other objects
*A good UI element should be able to communicate with other objects using a simple interface
*A good UI element should be able to work without using notifications
*A good UI element should be reusable


The more you can interact with a UI component independently, the more reusable the component becomes. Tests created for large applications are often difficult to maintain and are easily broken when new features are added. Tests created for a single UI component stand a better chance of being useful as a component develops. 
  
Generating reliable reusable code is always a good thing. 

--zootbobbalu

http://www.extremeprogramming.org/rules/unittests.html
http://www.ccsm.ucar.edu/csm/working_groups/Software/dev_guide/dev_guide/node13.html

----

Well, I thought publishing it at a site like cocoadevcentral.com might give it a bit more exposure. Sometimes posts like this get lost in the shuffle here. I only came across this after looking through the RecentChanges page.

-EricWang

----

That's cool you liked the guide. Trying to get something posted at a site with more traffic will be a little too distracting for me to handle. I totally agree that there are many useful articles getting lost in the maze of info, but Topics do help. Remember, this IS a wiki, so if you find something that needs a better link, there's nothing to prevent you from helping out!!

--zootbobbalu

There is indeed a need for some refactoring... the main page is getting cluttered (again). Maybe we should sort information presented there into a tools page (including dev tools, and wonderful things like this), class references, major programming topics (MemoryManagement, MultiThreading), et cetera?

-- RobRix

