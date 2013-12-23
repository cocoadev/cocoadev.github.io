---
layout: page
title: SaveImageFromDocument
---

Hi,
        I created a document based cocoa application. I am working with image file formats. I am able to open files without any problem. If any changes are made to the file and if I try to close the document, system asks for save and if 'save' option is selected for saving the file, "writeToFile" or "dataRepresentationOfType" method is called. In writeToFile method( overridden ) I am calling my own method for saving images, which will actually save the image, but system gives the message "Couldn't save this document". Here is the code snippet:

    
-(BOOL) writeToFile:(NSString *)fileName ofType:(NSString *)type
{
      [self saveFile];
      return YES;
}



Here [self saveFile] is the method(my own method), used to save the image.The image will be saved using this method. As I read if the above method returns 'YES', the error message should not appear. 
Also I tried by overridding the below method:

    
- (NSData *)dataRepresentationOfType:(NSString *)aType
{
      [self saveFile];
      return nil;
      //return [Image TIFFRepresentation];
}



In this [self saveFile] is the method, used to save the image. The image will be saved using this method. Instead of returning TIFFRepresentation, I am returning nil because system should not save the file. 
Is there any problem the way I am overridding the methods...OR.....Is there a solution by which I can use my own method instead of System saving the image file. Please give me the solution..... waiting for reply/replies.............THANKS A LOT......

----
With respect, I think you need to carefully read the conceptual documentation for the document architecture and the API reference for NSDocument (and NSDocumentController for that matter). As to simply giving you a solution - you won't typically find that kind of help here. We'd rather teach you to fish, rather than hand you one.

See also HowToAskQuestions and avoid MailingListMode.
----

Thank you for the referrence. I had gone through the documentation but could not solve the issue. I need to avoid the system message "Couldn't Save the document". Since long time I am working on this issue, Please could you atleast guide me towards the solution. Thanks.......

----
Read the documentation again. Your code suggests you haven't grasped even the basics. Specifically read about the methods you've overridden above. The idea is that you must return (in -dataRepresentationOfType:) your application's data encoded as an NSData object. **You** do **not** actually write the file out yourself. Give the data to Cocoa and it will do the right thing. The reason it is failing is because you're returning nil in this method. That's regarded by Cocoa is a failure and it says so. **This is all very clearly stated in the documentation and you would have noticed it if you'd actually read through it thoroughly.**

I will suggest this one last time: **RE-READ THE DOCUMENTATION.** Also, check out the example code included in the /Developer folder for a good demonstration of these methods. The lesson to learn is that you are not doing things the Cocoa way because you are failing to follow the documentation.

Read: http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSDocument_Class/index.html
And: http://developer.apple.com/documentation/Cocoa/Conceptual/Documents/index.html

When you've demonstrated that you've actually read the documentation thoroughly and tried the suggestions already offered above and actually have a question that can't be answered by researching just a little bit on your own, I'll be happy to help you further. One other thing - persistently asking to be handed an answer is very poor etiquette. You're asking the community for help and at least one well-published member is telling you very honestly that you need to go back to the documentation. Most of the members of this community who are experienced enough to offer help are also very busy developers with projects of their own. We are not willing to hold your hand or spoon-feed you code. We're happy to help you but we will no carry you. It's up to you to develop your researching skills as that is far more important than actually remembering the right commands and / or syntax. Develop your researching skills and you'll be a far better developer. Develop your etiquette when asking for assistance and you'll receive far better responses. This is what the HowToAskQuestions page is there to help you with. Please consider my suggestions.
----

Thank you for the update...I am happy with your feedback.. Actually I "do not" want system to save the file.I want my method to save the file instead of system that is the reason why I am passing 'nil' in -dataRepresentationOfType:, if NSData is returned system will save the file instead of my method.

----

(sigh) Some people just don't *want* to learn.

