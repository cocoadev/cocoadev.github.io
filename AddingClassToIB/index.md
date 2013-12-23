---
layout: page
title: AddingClassToIB
---

Hi

I am fairly new to Cocoa and IB but I have background in other programming languages. I undertand the MVC Concept and I have a hard time adding my classes created in XCode in Interface Builder 3.0 under Leopard.


I have read the Apple "Cocoa Application Guide" and the "Interface builder user guide". Every one of them tells me that IB3 should be smart enough to monitor the changes in XCOde and add the appropriate classes in IB, but it just doesn't, even with a newly created project. It does not work either if I use the File->Read Class file menu, IB does nothing, no errors, no console logs, nothing happens.

Same thing happens if I try to drag the .h file from the Xcode project to IB.

I have lost a lot of time on this simple task, can anybody help me on this ?

Here is what I am trying to do:

-Create an empty "Cocoa Application" Project in Xcode 3
-Add a file named "AppController.h" which is a sub-class of NSObject
-Add 2-3 outlets to AppController
-Define an action
-Add the class definition to IB 3.0

Here is the AppController.h content:
#import <Cocoa/Cocoa.h>


@interface AppController : NSObject {
    IBOutlet id imageView;
IBOutlet id getImageButton;
}
- (IBAction) getImage:(id)sender;
@end

----

If I understand correctly you are having trouble even getting the class in there the first time? 


 To add an object like AppController to you nib:


*AppController.h the header file into IB
*Go the the Library pane Cocoa>Objects & Controllers>Controllers and drag a Controller object into you nib file.
*Open the Identity inspector (Command + 6)
*Set the Class Identity to AppController


Now if you go to Connections pane (Command + 5) your outlets should show up there. At this point, you may have to manually synchronize this file with Xcode, (File: Synchronize with Xcode). I haven't really made enough controller changes lately to have noticed if it really does automagically track the file. 

I hope this helps, I think that is what you were asking.

++ Falco

----

IB 3.0 actually automatically syncs all of your classes. So if you're using IB 3.0, XCode 3.0, and obj-c, ruby, or python, you don't have to drag any files around or synchronize anything manually. You don't even have to do it the first time.

