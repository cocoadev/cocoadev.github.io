---
layout: page
title: BetterWaysToSaveRecursiveDataThanNSArchiver
---

NSArchiver is easily one of the easier classes to use for saving stuff....but it gets horribly slow when saving a lot of data (I mean a LOT).  Is there a faster way to do this?  I figure considering Apple uses it in IB, it would be faster.  Thanks 

----

The fact that Apple uses it in Interface Builder is one of the reasons it's rather limited, in fact. It's optimized for that use, and not always suitable for general use.

If your data can be flattened, writing a property list may work better for you.

If speed is your primary concern, MAKeyedArchiver may be helpful for you.

----

Thanks for the responses.  I have tried MAKeyedArchiver, but it was slower than using an NSArchiver (I imagine because it is key value based?).

----

would NSData's     writeToFile:atomically: work for you?

Well I write the encoded data from the keyed archiver.  Is there another way to save objects using only writetoFile skipping the encoding process entirely?

*not if you need to save objects, but do you really need to do that?*

----

I think you'll need to consider a custom archiving solution. Write serialization methods for your classes, that way you retain control of everything and can probably make things go faster, since you won't be writing a general-purpose serializer. This could end up being pretty difficult depending on how complicated your structure is, but I think that's the only other option at this point.

----


To be honest I was hoping it wouldn't come to that :).  I have a complex hierarchy of custom classes and a lot of data in each.... I have a feeling that looping through manually and saving them would be just as slow as an NSArchiver.  Thanks for your help.

