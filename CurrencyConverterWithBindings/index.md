---
layout: page
title: CurrencyConverterWithBindings
---



Problems with the currency converter bindings sample (I guess these could rightly be termed "Early Adopter Experience":

First of all, if I start the program, I get the error that my model does not support KeyValueCoding, since the tutorial only asks me to implement setters for dollars and exchange rate and a getter for the amount in other currency.

However, if I add the missing getters and setters for the attributes, it will start, but changing a text field will not update any other field -- it does update the model, and if the model was asked about amount in currency, it would correctly report the new result, but it is not asked.

----

online version (link OK as of Jul 03 2005):
http://developer.apple.com/documentation/Cocoa/Conceptual/CurrencyConverterBindings/index.html

local version (Xcode 1.5 standard installation):
file:///Developer/ADC%20Reference%20Library/documentation/Cocoa/Conceptual/CurrencyConverterBindings/index.html

----

Your model needs to invoke willChangeValueForKey: and didChangeValueForKey: on self for the generated key (i.e. the *amount in other currency*).

----

According to the documentation you only need to add willChangeValueForKey: and didChangeValueForKey: if you're performing Key-Value conformance manually. Anything subclassed from NSObject already has this set up.

When I run the Currency Converter designed by the tutorial it checks the value of the amountInOtherCurrency when the app launches, but never checks it again. How do we get the ControllerLayer to continually check the value of amountInOtherCurrency?

*This is why I said you have to invoke willChangeValueForKey: etc. -- even though the object inherits from NSObject, there is no way NSObject will know that the virtual value has changed.*

You were right

*You don't need to call willChange... if you simply use the accessor to set the updated value, instead of setting it directly.*

----

So why on earth did Apple not include that code? I spent an hour or more trying to get this tutorial to work myself, only to find that I had to use the solution mentioned above. Uh, HELLO APPLE! Don't you ever check your documentation to see if it works?!?! -- JaredWhite

*Alternatively it would seem that one can use setKeys:triggerChangeNotificationsForDependentKey:, though I have not tested it, and yes, it's not very cool of Apple to only provide us with half the details ;)*

*Details are here:* http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueObserving/Concepts/DependentKeys.html#//apple_ref/doc/uid/20002179 *however, I do not see how the notify will be triggered for the other two keys -- I read that ObjC will automatically send will/didChangeKey when assigning to ivars, but it does not work for me, nor would I expect it to, since ivar assignment does not go through any ObjC runtime?!?*

----

Hey, Apple's developer documention online is completely different than what came on the Xcode CD. The currency coverter tutorial contains more code, and a great deal more explanation. In fact, I believe the online example really works (at least as first glance, I haven't tried it yet).

So it looks like the documentation that comes with Panther is already out of date! Yeeesh. -- JaredWhite

As of July 2005, this situation should be ameliorated, at least vis-a-vis Bindings.

----

Precisely the reason I never look at the local docs. ALWAYS get it from developer.apple.com

