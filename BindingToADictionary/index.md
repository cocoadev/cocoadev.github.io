---
layout: page
title: BindingToADictionary
---

I'm trying to bind something (an NSDatePicker for now, but it could be anything) to an NSDictionary, like binding it to the user defaults, but I haven't been successful. What would be the way to do this?

Thanks.

----

What seems to be the problem? For example, this should work:


*1. Create a new project based on the "Cocoa Application" template.
*2. Open the MainMenu.nib file in InterfaceBuilder.
*3. Drop a date picker into the main window.
*4. With said date picker selected, open the inspector and go to the bindings panel.
*5. Click on the 'value' binding. Choose 'Shared User Defaults' for the 'Bind to' setting. Set the 'Controller Key' to 'values'. Type 'date' into the 'Model Key Path' field. Finally, make sure the 'bind' checkbox is checked.
*6. Save and switch back to Xcode.
*7. Build and run. Change the date in the date picker to some date of your choice. Quit. Run again. Verify that the date in the picker is the date you set.


Can you do this? If not, what goes wrong?

More generally, you really need to read some of the bindings-related documentation that comes with Xcode. There are some tutorials that go into much greater detail than the steps above. -CS

----
Thanks, but I'm trying to bind to an regular NSDictionary, not the user defaults. I've tried these bindings:

    
Bind to: theDictionary (NSObjectController)
Controller key: Selection
ModelKeyPath: date


And theDictionary is bound to a dictionary in the controller. I'm pretty sure these bindings aren't right though. 
What happens is the NSDatePicker just turns gray and won't let me change it.

----
Is it an NSDictionary, or an NSMutableDictionary? You can't change it if it's not mutable. Also, make sure that the dictionary actually has an entry with a 'date' key.-CS

----
Sorry, it's an NSMutableDictionary. And I tried setting an entry with a 'date' key in the -init method, to no avail...

----
Are you getting any warnings? Can you show us your -init method? It sounds to me like -init isn't returning self. Also, if you create the dictionary using one of the convenience allocators (-dictionaryWithCapacity, -dictionaryWithContentsOfFile, etc.), make sure that you're retaining the dictionary in your -init method. Also, try binding the date picker directly to your model object and using a key path like "dictionary.date". -CS

----
Oh man, I found the problem... I wasn't creating the dictionary at all. I just delared it in the class interface. I feel like an idiot. :p Thanks for your help.

