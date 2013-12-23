---
layout: page
title: KeyValueObserving
---




http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html

----

Reading through the preliminary documentation about the new bindings system I saw that values from the view are pushed to the model using KeyValueCoding, but afterwards, the view is updated using *key-value observing*, I have searched the documentation and Google, but cannot find any details about this new protocol.

Does anyone have any insight?

On a related note, I cannot make my view update its state when my model change -- I would assume that I would need to send out a notify here, which is why the key-value observing would probably be of interest.

*Yeah, you want to -setNeedsDisplay:YES or something. Notifications are probably best for this, as your model doesn't need to care what's listening.*

But ehm... how? I have one view that sets some value in the model which affects other values of the model, currently shown by other views -- how do I make these other views update? the idea with the binding-stuff is that I shouldn't need to write controller code to setNeedsDisplay: or similar.

*In your NSView subclass, when someone does a -setFoo:bar your -setFoo: method should call [self setNeedsDisplay:YES] (or -setNeedsDisplayInRect: if you don't need to update the whole thing).  I believe this is what happens in NSControl and its subclasses, for example.*

**No, this is not the intent of KeyValueObserving -- the idea is that a value in the model changes, and the view automatically updates based on this new value, without having code invoke setNeedsDisplay: or similar**

The mechanism works just fine, without any additional code written by the user (unless the user wants to give the system some extra handy information!).  Lets say I have a NSObject subclass, called (say) Employee, and the class has two instance variables; Name and dateOfBirth.  If a particular instance is displayed, using bindings, in a view, then all I need to do to ensure that the values are updated in the view is update them using an accessor with the correct name: e.g. [ self setName ] or [ someEmployee setDateOfBirth ].  I must do this even if I could access the variables locally - this ensures the KeyValueObserving mechanism picks up the change. 


If I want to have a third *derived* value on the class, such as age, which is calculated automatically when [ myEmployee age] is invoked, I clearly don't have a mutator (set accessor such as [ bob setAge ]) for it, since it is derived from the dateOfBirth value.  In this case I must tell the system that the value for the key 'age' has changed, using [ self willChangeValueForKey:@"age" ] before the change to the value it is derived from (dateOFBirth), and [ self didChangeValueForKey:@"age" ] after the change.  The UI element displaying the age will then update correctly.

    
-(void)setDateOfBirth:(double)newDate
{
  [ self willChangeValueForKey:@"age" ];
  [ newDate retain ];
  [ dateOfBirth release ];
  dateOfBirth = newDate;
  [ self didChangeValueForKey:@"age" ];
}


Please excuse any coding/naming errors in the above - I left my iBook at work today. :-(
--AlexBrown

----

There is also a mechanism for declaring derived key relationships that avoids the above coding, and replaces it with:

    

-(id)init
{
  ...

  self class] setKeys:[[[NSArray arrayWithObject:@"dateOfBirth"]
    triggerChangeNotificationsForDependentKey:@"age"];

  ...
}



Notice that you have to call this method on the class, not on the instance. This is poorly documented. It would be even better to put this in the "+ (void)initialize" method so that this only gets called once for the class. Something like this:

    
+ (void)initialize {
    [super initialize];
    self class] setKeys:[[[NSArray arrayWithObject:@"dateOfBirth"] triggerChangeNotificationsForDependentKey:@"age"];
}


See the linked documentation, or 

http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueObserving/Concepts/DependentKeys.html

for more details.

----

There is some documentation about observers as part of the NSObject API description. It was also briefly mentioned in one of the free ADC TV videos (New Cocoa API or similar).

----

Hey, I tried to watch the free ADC videos, but when I try, quicktime (e.g., not Safari) starts and always asks me for a login and password. I tried my ADC login/pass but no luck. What's the deal? Did I miss some documentation explaining the patently obvious? -- ShamylZakariya

----

The video is shown with Quicktime, but you need to login from the browser, since Apple find it necessary to require password, https and similar for something anybody can sign up to get... arg... does anybody know of a program which can grab the videos to disk (i.e. using rtsp)?

