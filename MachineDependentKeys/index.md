---
layout: page
title: MachineDependentKeys
---



* Machine-dependent keys

From the comments of MarcWeil in CocoaInsecurity:

*
I personally like the use of machine-dependent keys, and using this key (and the user's name) to generate a serial number. The key would, of course, be unique among all computers which almost guarantees that all of the serial numbers will be different as well. There is only one flaw in this scheme that I can find, however, and that is that you are forced to store the machine's code somewhere on the computer. If someone finds out where this file is, it would be quite easy for them to create a crack for the program that would modify this file with a static machine code so the same serial number (and a given name, of course) could be used on any computer that applied the crack. Of course, this is where blacklisting comes in handy, but the problem with blacklisting is that if the blacklist is stored on the server, people can block the connection with LittleSnitch? or another kind of firewall-like app, and therefore still get the program for free. If you store the blacklist locally in the app's bundle and simply add info to it as more versions are released, it would be no problem for someone to find the information and just get rid of it to make it look like an empty blacklist.
*

....and you may be back to worrying about the vulnerability to an InputManager

....discussion of such areas as NSObject's poseAs: method...

This appears to be one of the places where you run up against the iron law that there is no 100% security scheme.
Is it good enough for you? That is your call to make.

* Another Topic: Diminishing Returns from induced software disutility

 Discuss MikeTrent's point about diminishing returns if your protection scheme ends up making your software too difficult to use...

*oh, it's discussed, all right, in SoftwareSerializationPiracyDiscussion and in great detail in MakingSecureRegistrationCodes*

Also consider what happens when you go out of business and a (former) customer buys a new computer. And consider a customer considering that, while deciding between a product which uses MachineDependentKeys and a product which does not.

----

**Using machine-dependent (or not?) keys with NSUserDefaults and the keychain**

As a follow up to the  CocoaInsecurity thread I wanted to ask what to do after the user enters their serial number, i.e., how to save (somewhere on the disk) that the application is now unlocked.

But I realized that saving the actual user name and serial number to e.g. NSUserDefaults would do. Perhaps it would be safer to save the user name to NSUserDefaults and the serial number in the key chain, but then the user will be prompted to a "Application foo wants permission to access key chain bar" the first time he starts it (after registrating), and this may puzzle him...

Ideas anyone?

**Who cares if it puzzles him? Document it in the readme. Or with the registration information.**

If you don't want to be inundated with 'My mac was acting wierd so I deleted all my prefs and now I lost my registration code could you send it to me?' emails, save the name & serial to a file under ~/Library/Application Support/

----

What is the purpose of saving it to the keychain? You can still get stuff out of the keychain, it's not even that hard. And not everyone has the keychain set up to be automatically unlocked all the time; mine locks after five minutes, meaning your app would prompt me every time it started up. That would be annoying. I see no purpose being served by this and some hassle for the user. NSUserDefaults is fine. Yes, you could register another copy by copying the plist file, but you could also just write the serial number on a piece of paper and give it to your friend.

