---
layout: page
title: NSConnectionHelp
---

I'm having some trouble figuring out NSConnection.  I looked at the Widgets project already, and I would like to try to imitate it, but I can't figure out what exactly is doing the communication from client to server, or vice versa.

Basically, what I need help figuring out is how I could, say, have a slider have the same value as it's told.  While one could set up a socket, and send over the number, read the number, and set the slider's value to the number it read, I'd like to figure out a way to do this with NSConnection, as that's its job.

The trouble is, I can't figure out the exact syntax to call something which will let it happen as I described.  If someone can shed a little light, perhaps a tutorial on using NSConnection, it'd make my day.

Thanks,

-Charlie


----
In the discussion, IsNetworkingAsHardAsItSeems , I gave a very small introduction to NSConnection objects.  The code had some typos in it and that may explain some of the confusion (I was pulling code from different sources so variable names didn't sync up).  I went back and made some changes to it.
 
Things to note:
The IP address listed in the client code needs to be the IP address for the machine running the server code.  The TCP port numbers that I list as 12345 can be anything over 1023 as long as it doesn't interfere with any other service.  If you are distributing your software, you will have to put a great deal more care into the port number you use. 

Assuming your controller object on the server has an outlet to the NSSlider, you may have a method similar to this
    
-(void) setSliderValue:(int)inValue


You would have to make sure the server code is running before the client initiates a connection.  The server starts accepting connections as soon as it creates the NSConnection object in the awakeFromNib.  The client makes the connection when it creates it's NSConnection.  To actually send a method call through, you would run a command similar to the following from the client.  

    
[remoteObject setSliderValue:20];
 

----

Got it.  I can't thank you enough.

-CharlieMiller

