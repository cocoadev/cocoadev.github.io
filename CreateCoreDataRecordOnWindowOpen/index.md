---
layout: page
title: CreateCoreDataRecordOnWindowOpen
---




Hey Guys,

I am using Core Data to build an application, and have been following some tutorials, as I do not have much experience in this. I have created a Person entity with some attributes, and have a first and second name attribute. In interface builder, I have a table to show the first and second name in a column each (if anyone knows how to get them in the same column, I would appreciate knowing!). The problem I am having is this: I am using a sheet to enter in a new record, but I cannot find a way of attaching the Add action from my controller to the sheet opening, as I am trying to open the sheet, but if a new record is not added first, the text boxes on the sheet cannot be edited until I hit a button that is attached to the add action. Does anyone have any idea?

thanks guys

Rob

----

For your main question, the solution is fairly simple. What action is called that ends up displaying the sheet? How about before you display the sheet, you call the array controller's add: method yourself? Don't forget to remove the unwanted Person instance if your user hits cancel instead of OK on the sheet ...

For your side question (how to display two fields as one in a table view), it can be easy or hard. If you only want to *display* this information (ie, the user won't be able to edit it), just add an accessor method called "displayName" or "combinedName" or something similar, and bind the name column in your table to that key. If you want your users to be able to edit this field, you'll have to add methods to validate the entry and in the validation method, you'll need to parse the first / last name and update your first name and last name attributes accordingly ... not as easy when you have names like "Mc. Something" and "Van der Something" ... :-)

----
Thanks for the quick reply.

I'm quite new to all this, so I am not too sure about it all! The way I have it at the moment, I have multiple sheets being created in the app, so what I have done is create a sheetController.h and .m, and am instantiating a sheetController in interface builder every time I want to use a sheet, and connecting the buttons to it and it to the windows and setting the button actions once connected, know what I mean? Basically, there is no code, that I can find, to allow me to call the add method before.

With the second question, again been doing the linking in interface builder, so cannot figure out where to put the accessor method, would it be in the main .h file?

Also, despite having - (IBAction)saveAction:sender; at the end of appName_AppDelegate.h and having:

    
- (IBAction) saveAction:(id)sender {

    NSError *error = nil;
    if (!self managedObjectContext] save:&error]) {
        [[[[NSApplication sharedApplication] presentError:error];
    }
}


/**
    Implementation of the applicationShouldTerminate: method, used here to
    handle the saving of changes in the application managed object context
    before the application terminates.
 */
 
- (NSApplicationTerminateReply)applicationShouldTerminate:(NSApplication *)sender {

    NSError *error;
    int reply = NSTerminateNow;
    
    if (managedObjectContext != nil) {
        if ([managedObjectContext commitEditing]) {
            if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
				
                // This error handling simply presents error information in a panel with an 
                // "Ok" button, which does not include any attempt at error recovery (meaning, 
                // attempting to fix the error.)  As a result, this implementation will 
                // present the information to the user and then follow up with a panel asking 
                // if the user wishes to "Quit Anyway", without saving the changes.

                // Typically, this process should be altered to include application-specific 
                // recovery steps.  

                BOOL errorResult = [[NSApplication sharedApplication] presentError:error];
				
                if (errorResult == YES) {
                    reply = NSTerminateCancel;
                } 

                else {
					
                    int alertReturn = NSRunAlertPanel(nil, @"Could not save changes while quitting. Quit anyway?" , @"Quit anyway", @"Cancel", nil);
                    if (alertReturn == NSAlertAlternateReturn) {
                        reply = NSTerminateCancel;	
                    }
                }
            }
        } 
        
        else {
            reply = NSTerminateCancel;
        }
    }
    
    return reply;
}



the data will not save when the application saves. How annoying. Learning is tough!

----

Learning is even tougher, if you'll permit me to point out, if you try to go straight for the college material when you're still lacking your a.b.c.'s. As a professional, I advise you to drop the Core Data aspect for now and concentrate on the basics (which you are unfortunately missing, as evidenced by your last reply). With respect, I think you need to go back to the CurrencyConverter example and make sure you thoroughly understand every concept covered in that example before throwing in more advanced technologies such as CocoaBindings (which are, in turn, built on KeyValueObserving and KeyValueCoding) and CoreData. You're putting the cart before the horse, so to speak, and you'll learn very little that way. Follow the link to CurrencyConverter and master it, otherwise, there's not much advice people can give you that will truly help.


----

Yeah, I am aware of that fact, but someone I know needed an application for something, so I thought I would give it a go in an effort to learn something, I always find, for me, that it is easier to learn when you have a goal rather than doing examples. But thanks for the feedback, I will definatly be trying out the CurrencyConverter, but am still going to give Core Data a go, as if I dont then I will never come back to it!

----

May I say that such an approach is rather silly and dooms you to failure from the very beginning. It's like saying you don't know anything about music but want to compose a symphony to rival Beethoven ... but if you fail on your first attempt, you're giving up on music forever. Silly. :-)

----

Thanks for the help

