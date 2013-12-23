---
layout: page
title: PyObjC
---



"The PyObjC package provides the glue needed to interface the Python interpreter with the Objective-C language. The 'objc' module makes Objective-C objects and classes available as first-class Python citizens, it is not only possible to use Objective-C objects but you can also subclass Objective-C classes."  -- taken from the ReadMe file.

PyObjC based solutions have been in production in mission critical applications -- trading floors, scientific analysis apps, etc.. -- since 1996.  BBum has been a principal developer on PyObjC since 1994.   As of 2002/2003, Ronald Oussoren, Jack Jansen and Just Van Rossum are also principal developers and their efforts have vastly improved the accessibility and quality of the PyObjC solution.

PyObjC also supports GnuStep.

Development home page:  http://pyobjc.sourceforge.net

Pointers and tips (mostly from a Python perspective):  http://just.letterror.com/ltrwiki/StartCocoa

----

If you've never tried developing with PyObjC, you really ought to give it a chance... it really speeds development by cutting out the (loooong) build steps. --JacobKaplanMoss

----

And passes those build steps on to the end user! :)  The startup time on PyObjC apps is a little slow. *As of Python 2.3 and Panther, the startup times are greatly reduced.*

----

PyObjC reached 1.0 quality as of the fall of 2003.  1.0 means that PyObjC allows one to develop full fledged Cocoa applications with or without ProjectBuilder / XCode using Python instead of Objective-C.   The Python<->Objective-C bridge is complete and supports such complex idioms as structures, pass-by-reference, and other C idioms that typically do not translate well to scripting languages.

----

If I use this, do I have to require my users to install the PyObjc package too, or is there some kind of embeddable runtime I can include in my AppBundle?  --OwenAnderson

I believe that if you use the --standalone argument with the bundlebuilder.py which is included in PyObjC, then it will build you an application which doesn't even require Python to be installed ... but I haven't actually tried that myself --CliveJevons

----

I'm a little confused about the PyObjC stuff.  What I would like to do is to create a Cocoa app with ObjC and have a python interpreter embedded in the application. Does the PyObjC have an interpreter in the framework?  I know that PyObjC can allow you to write cocoa apps in python, and that is not what I want to do.  I'm doing this as more of a learning project for objectiveC/Cocoa and producing something that is useful for me. 
-Fluidic

I believe that if you include /System/Library/Frameworks/Python.framework in your project, the standard Python API will allow you to write an interpreter.  There's plenty of documentation at http://www.python.org.  Or Google.  --RossDude

----

Has anyone figured out a way of using the new Controllers with PyObjC? I can't figure out how to add the properties to my Python code so that the controller is then able to access them. --CliveJevons

Found my own answer ;) I'm using the NIB/CLI route for developement. Found the answer in the News section of one of the releases. I created a class derived from NSObject, instantiated and bound it to the delegate outlet of the File's Owner. Then created the classes again. Now added two methods (def entries (self) and def setEntries_(self, newEntries)) and below each of theses line:     entries = objc.accessor(entries) and     setEntries_ = objc.accessor(setEntries_) ... that did the trick nicely. I found out afterwards that the accessor only really seems to be needed for the setter, because ObjC requires that method to return void, but by default python returns the value the last expression / statement returned. Anyway - it works and it ROCKS! ;) --CliveJevons

Incorrect. Python returns the builtin singleton     None if a fuction ends without an explicit return statement. To add properties to a class, one must explicitly declare ivars with statements like     foo = objc.ivar('foo') in the class body, or inherit from     NibClassBuilder.AutoBaseClass and declare outlets in your nibs.

----

How does one cope with methods that take (void *) arguments? I've tried changing the selectors to take n^v arguments but I get an "unknown typespec 76" error, and b.bum on http://www.cocoabuilder.com/archive/message/cocoa/2004/6/20/110180 suggests there are inherent problems with void * arguments.   Is there a way to do it?  And does it just involve setting the selector signature, or wil it have to go into C wrappers, like the way NSData seems to do it to receive (void *) bytes for the initWithBytes methods?   

What I'm trying to do is, I'm trying to access the writeSync:length: selector in the IOBluetoothRFCOMMChannel class (in the IOBluetooth framework). This method takes a (void *) bytes argument to define the data to write to the channel. I've tried making an NSData object using initWithBytes:length: and then getting its bytes by calling its "bytes" selector, and passing this value to to writeSync:length:.  However this is obviously not the same thing, as it tells me this is a "buffer" object, not a "pointer" object.

 *- Er, never mind, I figured it out -- just used      *S (string, unsigned short) for the param part of the signature, and it worked.      n*S worked as well but I guess the     n is unnecessary here. So now writeSync_length_('hello', 5) works.  *

This worked for me so I thought I'd put it here: --follower

    
import objc
objc.loadBundle("IOBluetooth",
                globals(),
                bundle_path=objc.pathForFramework(u'/System/Library/Frameworks/IOBluetooth.framework'))

# Thanks to Ronald on the dev-list:
m = IOBluetoothDevice.openRFCOMMChannel_channel_
signature = list(objc.splitSignature(m.signature))
signature[-1] = objc._C_OUT + signature[-1]
objc.setSignatureForSelector('IOBluetoothDevice', m.selector, *.join(signature))

# Thanks to poster on wiki
m = IOBluetoothRFCOMMChannel.writeSync_length_
signature = list(objc.splitSignature(m.signature))
signature[-2] = "*" # See <http://www.cocoadev.com/index.pl?PyObjC>
objc.setSignatureForSelector('IOBluetoothRFCOMMChannel',
                             m.selector,
                             *.join(signature)
                             )

del objc




*- That works for strings but doesn't seem to work when transferring binary data.*

