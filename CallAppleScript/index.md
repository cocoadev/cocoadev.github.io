---
layout: page
title: CallAppleScript
---

I have used the below mentioned code as a sample:
    
on show_message(user_message)
   tell application "Adobe InDesign CS3"
      display dialog user_message
   end tell
end show_message


and all the related code suggested in CallAppleScriptFunction Discussion using cocoa framework.
This code is working fine and displayed the desired output.
but when i  implement more command  into my handler function of script.Then they are not executing. Here is the code of my script.
    
on show_message(user_message)
   tell application "Adobe InDesign CS3"
     set myDocument to make document 
 display dialog user_message
   end tell
end show_message


 when i run this script from the code,statement :set myDocument to make document  is not creating any document while rest of the statements are working fine.
could anyone suggest me what is the cause for this?
there is no error in the statement it is a valid applescript command for Adobe Indesign application.

Thanks,
Vinod kumar

----
Please, next time you create a NewPage, take a moment to read the instructions first. (I've fixed up your entry to remove remnants of the NewPage page that were left there due to your failure to read the instructions, and also formatted your code appropriately. Please spend five or ten minutes doing something nice for someone else.)

Have you tried running the code in question from the AppleScript editor? -CS

