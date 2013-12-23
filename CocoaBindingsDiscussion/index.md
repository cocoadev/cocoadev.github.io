---
layout: page
title: CocoaBindingsDiscussion
---



How does key value binding work? It seems to know more than it should; I mean, as though encapsulation is being violated. There seems to be a notification of some sort being called, or maybe it is a direct message with some kind of intermediary object, but what process is tracking the changes? I can see if you use key value coding, but what if you change the ivar with a normal accessor? How would the protocol be activated? I am sure it is clever, but who or what has their nose in my model?

----

If you change the ivar yourself, you need to invoke will/didChangeValueForKey: on self, and your superclass (NSObject) will check if any observers have been added to your object for this value, and if so, notify these.

----

Someone above said:
"How does key value binding work? It seems to know more than it should; I mean, as though encapsulation is being violated. There seems to be a notification of some sort being called, or maybe it is a direct message with some kind of intermediary object, but what process is tracking the changes?"

I have not investigated, but there was a post on Apple's cocoa-dev mailing list claiming that the system secretly subclasses your classes, and the subclass sends notifications.  It was not a very happy post.

----

Here is the post, and some valuable added comments by the original author: http://www.bignerdranch.com/Resources/nscontroller.html

Strangely I cannot get the automatic key-value notifcations sent for my methods, even though I am KeyValueCoding compliant -- but I have to issue my own will/didChangeValueForKey:

*You're probably using -takeValue:forKey: to change a value rather than the new -setValue:forKey: introduced with KeyValueObserving.*

----

Well my take is that this is not especially to help with Cocoa at all, but is a way to extend the power of AppleScript and allow non-devs to do more. I think Apple is paving the way to build a tool to compete with Visual Basic. All this about making it easier to write applications glosses over the generic usage -- textfields only, it seems to me. Sorry, Apple, but real developers work with more than just text and numbers. Ever heard of audio, images and 3D content?

*Sheesh, what's your gripe? You want it to do more? Make it do more. For me, something like KeyValueBinding is useful. And I do audio, images, and 3D content--so whether it's for textfields only or not, it lets me focus on the audio, images, and 3D content rather than focusing so much on basic interface nonsense. This is something I've been wanting since Jaguar came out. I, for one, am pleased.*

See NSImageView's bindings.  It supports image data directly as a part of the bindings.  There is nothing about the Controller Layer that is either hardwired to strings and numbers or hardwired to NSTableView (or any other UI widgets).   It is a generic set of features implemented on top of KeyValueCoding and KeyValueObserving.

----

I can't get the simple NSObjectController/NSSlider/NSTextField demonstration to work in IB. I do everything action for action in the QTMovie ADC provides for XCode's and IB's new features, but when I run the interface the text field says "no selection" and everything is not enabled. Anyone else run into this?

Ok I figured it out. You have to set the NSObjectController instance to automatically prepare the content (the model part of the Model-View-Controller design pattern). The content is the NSSlider and the view is the text field. To set the controller object to prepare the content, select the controller instance in the instance tab view, hit command + "1" to edit attributes and check "automatically prepare content". 

----

Whether bindings are "useful" or not is pretty personal an opinion, but in general they add overhead, most of which you don't see (unless you use bindings manually or write custom bindings for your own objects) in exchange for a lot less custom code.

Bindings, like everything else in Cocoa and Objective-C, rely on messages. The only magic that I can see, so far, involves some kind of runtime hijacking of your accessor methods (those that conform to the Key-Value-Coding rules/protocol) in order to send more messages which facilitate the real work in the whole KV-whatever architecture and bindings. So essentially when a change is made using the accessors, observers are sent messages (not using "notifications" like the documentation says, unless you mean a message as a generic notification) -- specifically using the -notify... method.

Controller classes descended from NSObjectController take the idea of a controller class to its most generic. All messages are generated dynamically -- selectors are constructed at runtime using the names of attributes/ivars. [I'll have to help finish this later.]Well my take is that this is not especially to help with Cocoa at all, but is a way to extend the power of AppleScript and allow non-devs to do more. I think Apple is paving the way to build a tool to compete with Visual Basic. All this about making it easier to write applications glosses over the generic usage -- textfields only, it seems to me. Sorry, Apple, but real developers work with more than just text and numbers. Ever heard of audio, images and 3D content?

*Sheesh, what's your gripe? You want it to do more? Make it do more. For me, something like KeyValueBinding is useful. And I do audio, images, and 3D content--so whether it's for textfields only or not, it lets me focus on the audio, images, and 3D content rather than focusing so much on basic interface nonsense. This is something I've been wanting since Jaguar came out. I, for one, am pleased.*

See NSImageView's bindings.  It supports image data directly as a part of the bindings.  There is nothing about the Controller Layer that is either hardwired to strings and numbers or hardwired to NSTableView (or any other UI widgets).   It is a generic set of features implemented on top of KeyValueCoding and KeyValueObserving.

----

I can't get the simple NSObjectController/NSSlider/NSTextField demonstration to work in IB. I do everything action for action in the QTMovie ADC provides for XCode's and IB's new features, but when I run the interface the text field says "no selection" and everything is not enabled. Anyone else run into this?

Ok I figured it out. You have to set the NSObjectController instance to automatically prepare the content (the model part of the Model-View-Controller design pattern). The content is the NSSlider and the view is the text field. To set the controller object to prepare the content, select the controller instance in the instance tab view, hit command + "1" to edit attributes and check "automatically prepare content". 

----

Whether bindings are "useful" or not is pretty personal an opinion, but in general they add overhead, most of which you don't see (unless you use bindings manually or write custom bindings for your own objects) in exchange for a lot less custom code.

Bindings, like everything else in Cocoa and Objective-C, rely on messages. The only magic that I can see, so far, involves some kind of runtime hijacking of your accessor methods (those that conform to the Key-Value-Coding rules/protocol) in order to send more messages which facilitate the real work in the whole KV-whatever architecture and bindings. So essentially when a change is made using the accessors, observers are sent messages (not using "notifications" like the documentation says, unless you mean a message as a generic notification) -- specifically using the -notify... method.

Controller classes descended from NSObjectController take the idea of a controller class to its most generic. All messages are generated dynamically -- selectors are constructed at runtime using the names of attributes/ivars. [I'll have to help finish this later.]

*Editor's note: A pragmatically-oriented Q-and-A about how to use bindings and KVC in persisting parts of a database model has been relocated to KeyValueCodingOperators, also part of the CocoaBindings complex of topics.*

----

Am I the only one who has noticed that Apple doesn't use bindings in any of their Cocoa apps? Even updated versions in Tiger.

(NOTE: This is based off the fact that there isn't any sign of them using bindings the their NIBs, so they could be creating them programmatically.)

----

Something I was wondering (which isn't related to the current discussion but it core to bindings):  is there a listing of the aggregate bindings functions?  By this I mean the strange "@sum" key-paths and similar things which I have seen in some bindings examples.

----

[http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/Concepts/ArrayOperators.html]

----

Despite what the docs say, you cannot bind to, for example, the @count property of an array. You have to use an NSArrayController and bind to that.

----

I hate CocoaBindings, but possibly because I never took the time to learn it because my software has to be compatible with 10.2 (at least for the time being). I'm not as good visualizing how the UI and code should interact.. I'd rather just code it all :)

----

Hey, even Aaron hated Cocoa bindings at the beginning, see
      http://www.bignerdranch.com/Resources/nscontroller.html

----

I love the cocoa bindings idea. But I must admit that usage is not always really simple (specially NSPopupButton bindings!).

However, once you're familliar with (and if your app is not supposed to run on 10.2), bindings are excellent. They allow you to implement features like editing multiple items (master detail interface) listed on a table view without any lines of code.

I personally think that bindings are great for master detail interfaces, user defaults, but bad (too complex) for complex user interface, custom controls and NSPopupButtons!

----

Perhaps you should reevaluate your own understanding of bindings, then. ;-) I have a *very* complex interface with some extremely detailed, deeply nested, and highly customized controls that use bindings very well. The difference is I went the extra mile and figured out manual bindings. It's a little extra work than using IB but nowhere near as much work as doing it all with glue code.

----
Sure, bindings are great for deeply nested interfaces. Customized controls need some extra work to work with bindings (but I admit it's normal). But if you want some custom behavior with bindings, its often quite complex (or not possible).

And, I had forgotten one area where bindings are really bad : localization. It's already painfull to edit "normal nib files" (I mean, without bindings) after localization process. Bindings just make things worse.

----
I get your general meaning. However, both of your statements above also reflect lack of proper research and documentation reading. The answers are there and bindings works wonders.

----

Anything that minimizes developer effort and doesn't limit or obfuscate code in order to be effective is A Very Good Thing�.

I tried binding on one of my GUI<nowiki/>s. Phase one worked beautifully. I was able to eliminate reliance on over 60% of the glue code in my controller class. Significant. Phase II was a miserable failure. But a failure doesn't mean that CocoaBindings is bad. Just limited. And all APIs and architectures are limited. I'm glad I learned something, and I'm disappointed that bindings wasn't able to help me. I ended up having to roll back my code to pre-bindings. But I certainly learned more than I knew before.

My specific issue is a one-to-many relationship in my model. The objects are stored in an NSDictionary. My getter returns an NSArray. The API allows addition of objects to the relationship by specifying the object itself, but NOT the key.

Since I my getter does not return an NSMutableArray, the KVO/KVC compliance rules require that I implement a couple methods:

    -insertObject:in<Key>A<nowiki/>tIndex: and     -removeObject:in<Key>A<nowiki/>tIndex:

Obviously, since I store the objects in an NSDictionary these methods are nonsensical. I cannot guarantee the contract implied by the index.

The root of the issue is that bindings has no way of knowing how I want to manage the order of the objects in my collection. All GUI paradigms for displaying many objects have a sense of order. I have to manually resolve the issue in my glue code. There's no way an automated system can do that for me.

Of course, I didn't realize that until after I tried... D'oh!!

I could add an NSMutableArray to my model. Then update the getters and setters to return the array, and update both the array and the dictionary. I considered it, but decided not to since the array would be used NOWHERE else in my model object. I'd feel dirty if my 'pure' model object had artificial code in it.

Now if I hadn't already written the GUI without bindings, and had to consider the effort involved, I certainly might reconsider.

--TimHart

----

Bind to an object that has a category extension and Bindings break it.  I HATE THEM!  The feel like an MS tool, great concept, little testing, less real use, and poor documentation.  I'm still trying because I have already put so much time into trying to use them, I want to see something for my work.  I probably won't be able to get blood out of this rock though.

-J.O.

----

Could you clarify the above?  Is the binding on one of the category methods or to any method of the class (original or category addition)?

----

I think J.O. is blaming categories for some other problem he's having. I use categories to get bindings to work all the time-- it's a GREAT way to make the bindings layer work with the data model, without making changes to the "ideal" data model. Look at TimHart's example above. What I would do in that situation is make a category for the data model object (call it BindingsLayer, and define it in the view controller class). Create a method for this category which takes the dictionary values, sorts them, and returns an array you can bind your UI to. You may need to put a little extra code in to make sure key/value observing works okay, but that's basically all there is. Your bindings should work great, and as far as the rest of your application is concerned there's nothing different at all about the data model.

I used to struggle with bindings myself; this is one of the tricks I eventually figured out which just makes everything a lot easier.

----

I have a comment about the valuePath binding: I give it a proper path and it loads the referenced text file just fine. I have *Continuously Updates Value* checked and 
*Allows Graphics/Allows Multiple Fonts* unchecked to make it a plain text editor. However, any changes I make in the NSTextView are not written to the file. How do I trigger this behavior? Is this possible with Bindings?

----

If you know the path, it is pretty trivial to save the text yourself - once you have all the pieces together.

[myTextView textStorage] string] writeToFile:pathToFile atomically:NO]

[[EcumeDesJours

----

I was rather wondering if you could do this with bindings. Everything else seems to be two-way, but this apparently is not.

I've had some pretty amazing success using bindings for a variety of complicated things. For instance, using packages to store 'journal entries' in the main project data while saving images to and loading them from image files inside the package (to prevent loading and saving out all the data including the images over and over). It was a bit of a struggle to figure it out, but it did eventually require me to modify my model object a bit to make it flexible enough for this task.

I think what I'll have to do is take my "Entry" object and modify it so its -content and -setContent methods both read and write to the actual file. I really do think valuePath is one-way.

