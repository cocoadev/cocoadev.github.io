---
layout: page
title: MakingSecureRegistrationCodes
---



Suggestions so far:


* http://macromates.com/sigpipe/archives/2004/09/05/using-openssl-for-license-keys/ 
* http://www.esellerate.net - uses software activation
* http://aquaticmac.com/ - Uses public-key crypto, BSD licensed


eSellerate is a product registration system, which you can use to take care of credit card purchases for your apps. They have a nifty integrated registration SDK, which can save a lot of time.

-- DustinMacDonald

----

I use eSellerate for all my apps, so does Unsanity, Freshly Squeezed Software and countless other Mac software houses. I do recommend it. -- MatPeterson

----

I do as well. It's been a good partnership that's held up a good size chunk of my business logistics with very little maintenance for a reasonably-sized cut of the profits. I've always gotten exact assistance any time an issue has come up, either with integrating with their engine or with customer questions pertaining to the buying process. I haven't had a negative experience (that was their fault) yet, so I have to recommend them as well. - JNozzi

----

There was a tutorial about registration on CocoaDevCentral awhile back. But if you're looking for easy, eSellerate is the way to go.

----

However, the issue has spawned a lot of discussion about relative merits of different schemes. A summary:


* *All schemes can be cracked*
* Using externally-linked libraries seems more vulnerable, having a single, obvious point-of-failure
* Public-key encryption systems may be especially vulnerable to replacement of the public key, unless it is obfuscated throughout the entire app
* Any scheme accessed via Cocoa calls is vulnerable to attack via an InputManager
* Activation schemes are not necessarily evil. Some implementations, however, can be.


see also CocoaInsecurity and ProductSecurityStrategies and SoftwareSerializationPiracyDiscussion

----**The Discussion**----

Consider using eSellerate. http://www.esellerate.net -- I don't work for them, but I use them for my products. Without software activation, anything you make can be easily reverse-engineered and bypassed. Some will argue that software activation is the root of all evil - and they may be right - but after a year of being pirated left and right with abysmal sales, my sales suddenly skyrocketed once I started requiring serial number activation. I get very few complaints (about one a month), but as long as you're up front from the beginning (as in, an obvious statement on the product page), you'll filter out those who absolutely hate the idea. That's just my personal experience. Take it with a grain of salt.

----

I disagree about product activation being necessary for security.  Take a look at the AquaticPrime system at --
It's still in development, but should be released within the next month or so (July '05).  It provides one-way serial numbers (i.e. computationally infeasible to generate fake serial numbers) and automated online purchasing through any payment processor (Kagi, PayPal, etc.) without product activation.  And it's totally free.

*How is that solution secure, when it's open source. Couldn't anyone just put in the criteria you entered, and then have serial numbers that work?*

**Take a look at this page describing the RSA cryptosystem: http://en.wikipedia.org/wiki/RSA **

It's secure because the developer has a unique private key on their computer, and the chances are zero to none that the cracker could recover that private key.  However, there are other flaws in this scheme, as I have outlined at the bottom of this page. --OwenAnderson

**ANY serial number scheme can (and eventually will) be cracked. Period. Authorization makes it necessary to either check with the software maker so as to limit the usability of a serial number or break out an assembler and modify the binary. This doesn't stop piracy for more popular apps (crackers, etc.) but it makes it one hell of a lot harder. I've tried various schemes and, without activation, they had all failed (generated serials abound). This has *stopped* piracy for a year and a half for me personally. No cracks on warez sites, few complaints, plenty of sales. Hands-down, it's a huge success.**

*The fact that you say generated serials abound means you didn't use a public-key cryptographic system properly, if at all.  I would say this precludes you from discussing the effectiveness of such systems.*

Does that include serials generated using public-key encryption of hardware details? -- KritTer

*It sure doesn't, but there are two important questions: 1) Where is the code to generate the serial stored? 2) Where is the *private* key stored? If 1 = the app, then one crack will break any app that uses this scheme. If 2 = the app, then ... one crack will break any app that uses this scheme.*

----The private key should be nowhere near the app, half the point of public key cryptography is that you only need the public key to verify a signature

*And any app will break if you crack it. All of them. Bar none. -- KritTer*

----

AquaticPrime sounds nice, but I can already see how to break it.  An enterprising cracker will find the public key stored within your application and swap it for his own public key.  Now, he can generate his own license code by hashing his own name and encrypting it with his own private key.  Having done this, he now has cracked version of the application and a valid username/license code.  Even worse, because the code is self-generated rather than a legitimate one that he is giving away, knowing his username doesn't help you track him down at all. 

Sorry to be pessimistic and all, but them's the fact.  This system would probably work for most casual programs (since there is a degree of effort necessary to crack it), but high-piracy target (games, expensive apps) should probably look into another solution.  --OwenAnderson

*Which is why the public key code is automatically generated with key obfuscation techniques to prevent key replacement.  I've thought about this quite a bit, and when the application+framework is released, it will have some very effective tips to prevent software modification.*

But then you're back to relying on obfuscation rather than cryptography, which is a step down in terms of security.  Hence, in the end, you're supporting the earlier point that activation-based registration systems are, in fact, superior to yours.  At least for them, if someone defeats the obfuscation, they can detect it and reject the registration. --OwenAnderson

*Since when are activation-based systems any more resilient to cracking than public-key systems?  Both are vulnerable to an extent, and neither necessarily holds any advantage.  You're making an assumption here that isn't true.  An attacker could easily bypass the validation and connection functionality of an activation-based system.*

**"... to an extent ..." Yes, but again, I'm referring to the DEGREE of the extent. The *fact* is that it's easier to bypass serial numbers without activation than it is with. The EXTENT you have to go to for the former is less than the latter. Which is why I happen to advocate FOR activation keys. The only people who have anything to hide (citing privacy 'issues') are those who are trying to hide things. Anything a developer can do to protect his or her investment in time is a bonus. This particular scheme, however, lends no more security than a flat home-grown serial generation scheme. Period.**

"The only people who have anything to hide (citing privacy 'issues') are those who are trying to hide things." 

Treating your customers, even privacy-minded customers, as if they were criminals is no way to make a living.

*With identity theft at an all-time high, I think this is a terribly irresponsible way to be handling sensitive personal information.*

Identity theft? Businesses keep customer records. That's an inescapable fact. The more provable sales information you have the less chance of being bitten by an IRS audit. This is how the world works, idealism or no. If I purchase any shareware, I'm *always* expected to give my name and financial (payment) information. That's sort of how buying things works ... If you try writing a check in most department stores (an untrusted payment method these days), you're asked for your driver's license. If you try to cash a check at a bank with which you have no account, if they'll cash it for you, many expect a *finger print* on the check (Bank of America, for instance has a fingerprint inkpad at every teller window). Those who spend time *and very often money* developing software are just like the DMV, the department stores, and the banks - they cannot survive the kind of losses they'd see if they automatically trusted everything. Again, my software was pirated left and right until I implemented activation. Suddenly my sales skyrocketed and I haven't seen it on warez lists for ages (a fact, whether a nice one or not). If you don't agree with activation, don't use it. Simple enough. Further, any time a user has ever reached their activation limit, I've always bumped it without question. I have one user whom I noticed has had no less than TWELVE computers activated within the last six months. TWELVE. The license allows for TWO. I didn't ask, I didn't pester, I just added another activation. Sooner or later, I'll void a few of the earlier ones and deal with whatever conflict arises, but I'm doing nothing wrong and I'm most *certainly* not treating customers like criminals, even if their activity looks pretty damning. Activation scheme != criminal treatment.

Expecting paying customers to show proof of purchase before installing on more computers does not imply a cavalier attitude about the security of their personal information. The only people reluctant to send a unique machine hash for validation are the people who are trying to steal license keys. No personal information is sent either way once the purchase has been made. The machine hash is exactly that - a hash

*I dispute this claim:  "The *fact* is that it's easier to bypass serial numbers without activation than it is with."  What is your basis for such a claim?  Sales statistics aren't corroborating facts here.  What if the attacker just modifies his hosts file and sets up a fake activation server?  That is pretty easy to do, since all the functionality is built into the OS.  Much easier than program flow modification to work around obfuscated asymmetric key loading.*

I don't know of a single activation system that sends sensitive information. This whole argument emerges from my suggestion to the OP that he consider using eSellerate.

*This is not really the place for privacy issues to be discussed.*

In response to: "What if the attacker just modifies his hosts file and sets up a fake activation server?"  I would contend that this is significantly more difficult than cracking most or all serial schemes.  Especially since many activation code systems involve the generation of a unique response.  In that case, the cracker would have to crack the serial, reverse engineer the activation protocol, write a server for it, and crack the activation code generator.  And, most importantly, he can't redistribute that crack.  It will only work for him. --OwenAnderson

----

The webpage for aquatic mac states that the framework will be available in a cocoa flavor.  If all of the security stuff funnels through known objective-C calls, it will be extremely easy to override via an InputManager (or just by swapping in a different framework?  How can you check if the code that looks for swapping is in the framework?).

*How does any system prevent against InputManager/swizzling-type (or find and replace) attacks?  eSellerate isn't invulnerable from these either, Obj-C or not.  In fact, I would go so far as to say eSellerate is insecure and has been compromised for quite a while now.  To answer your question, the algorithm is not complex (rather, it's simple and hopefully secure) and a pure C implementation in library or code form will be trivial and available within a reasonable time period.*

Well, if you know how to swizzle an inline function, I'd be interested to hear about it.  :-)  I think not using ObjC is a pretty basic rule.  You're really going to have to do a statically linked library, it's silly to claim that a pluggable framework is in any way secure.

*A find and replace will take care of any inlined static library in pretty short notice.  There are ways to verify the validity of any framework that you are using in your application, but they take a bit of care to use and probably should be discussed in another place.  You still haven't proved your claim that activation-based systems are better at preventing cracks than any other type of registration system.  All that we have determined is that nothing is perfectly secure, but some ways are better than others.  If there is a system that is more effective at preventing generated serials (which seems to be the favored method of piracy that activation hopes to prevent), why not use it over an expensive proprietary solution?  By providing a simple method that can be implemented in any language and is totally free from ridiculous fee overhead and license restrictions, I'm hoping to fill a gap that I feel currently exists.  The existence of this system in Obj-C (and PHP and C and...) form is probably considered by most developers to be "convenient" rather than "silly".*

Just to clarify, I'm not the same person who posted the last response.  I always sign my messages.  I did not claim that activation-based systems are perfect, nor do I deny that your framework will probably be useful.  I merely point out that they both, in the end, rely on making sure the cracker can't access some critical piece of data that can't be encrypted.  In activation-systems, this is the activation response code, and in yours this is the public key.  It seems to me that no matter how good your obfuscation is, it will always be easer for the cracker to obtain the data from your system than from theirs.  Oh, and I would be very interested to know what methods you have to protect against ObjC replacement techniques. --OwenAnderson

----

On a semi-related note, I suggest people not rely on the SHA-1 hashing method mentioned in the original article.  There have been a number of attacks on that hashing method recently, and while it is probably still good enough for these purposes, the trend is disturbing to many cryptographers.  I suggest people investigation the Whirlpool hash instead.  http://pag inas.terra.com.br/informatica/paulobarreto/WhirlpoolPage.html --OwenAnderson [remove the space in the domain] 
CocoaDev is flagging this post as spam due to the word "pa gina" (without a space). --ACoolie

*I think an attacker would have better luck breaking into your house and stealing your private key than finding an SHA1 collision in any amount of time.  The SHA1 collisions have all been rather contrived at this point, and while it is highly suspect for long term security, it is not entirely useless yet (MD5 however, is another matter :P ).  Furthermore, has there been any significant cryptanalysis on Whirlpool at this point?  Being based on a supposedly secure block cipher isn't enough for me to believe that it is effective.  For all we know, the XSL-type attacks will be improved and extended in the same time period that SHA1 collisions become trivial.*

WhirlpoolHash was selected by NESSIE (the European equivalent of the AES search) as a final cryptographic primitive, and they announced that they found no flaws in it.  But at any rate, if, even in the long run, we are going to move away from the SHA hashes, should we not start by encouraging people to investigate other alternatives? --OwenAnderson

*No one ever finds flaws until it's too late :)  Remember that the NSA declared SHA-1 to be secure, and Ron Rivest declared MD5 to be secure, etc.  I understand the want to move away from the SHA-1, but I'm not sure if the threat of collision in a few years is enough to add this amount of complexity the process.  SHA-1 is widely used and supported, and thus very convenient and still relatively secure.  How do you feel about SHA256/512?  SHA384 is pretty worthless, but I would at least consider migrating to either Whirlpool or the other two.  A big problem is the installation of modules and the execution time in a language such as PHP.  PHP supports SHA1 natively and it's pretty quick, but I would have to use the mhash or cryptopp modules for the others.  This means a stock OS X web-server configuration can no longer generate license files.  Any experiences with regards to installation and/or performance of mhash/cryptopp?  I can probably run some tests myself later on, but I'm interested to hear what others have to say about them.*

----

This is bullshit. SHA-1 collisions are not important for license key generation using public key cryptography. Even attacks on MD5 signing schemes required creating two specially crafted documents and then "sending" one of them to signing authority. This means that an attacker must create two registration names that have the same SHA-1, then purchase your product using one of the names. Then he can use both names with the same key. Nice! How's that important if he already purchased a key? Also, most registration names can contain only defined set of characters and limited to a certain length.  What's the probability of SHA-1 collisions with those limitations? Very, very, very extremely low. The discussion of theoretical collisions of SHA-1 applied to registration keys is pointless. -- DmitryChestnykh

----

Right, at least three people are participating here.  The point is that ObjC replacement techniques are _really_easy_ to use, and swapping in a dummy framework is even easier.  If you'd like to be taken seriously, and I'd like to take this work seriously, I'd suggest never releasing an ObjC version.  To use such a thing is only a very weak illusion of security, and will hurt your credibility.  

*I think the illusion of security exists with any type of system that is released.  Hopefully these points will be elucidated for developers when they are selecting a system.  You've got to keep in mind that up until this point, a large portion of developers are using eSellerate, who are not only vulnerable to serial number generation but also overall activation bypassing for every application and find and replace tactics on their static library.  Eliminating at least some of these points of failure makes it easier for developers to retain some of their money against the casual piracy in the marketplace.  The other x% who will go to the lengths of using an InputManager type attack or a cracked application in all probability would go without if they didn't have this avenue available.  These sales aren't "lost" in the same way that pirated serial number users are.  There is no reason (IMHO) to remove the convenience and usefulness of an Obj-C implementation just to prevent what wasn't going to be a sale anyway.*

**Okay, one more time:  Find and replace on an executable takes a non-casual user.  Swapping in a framework takes someone who can use the finder.  Dropping in a single input manager that cracks _all_ apps that use your framework is also darn easy.  If you use ObjC, the attacks are _easy_.  I'd _much_ rather use eSellerate than anything written in ObjC.  If you release an ObjC version, I'm going to conclude that you are not a person I want to delegate my security decisions to.**

*Fair enough.  However, be aware that eSellerate has a drop-in library replacement that cracks _all_ apps that use eSellerate activation.*

Are cocoa apps often trivially vulnerable to swizzling attacks?  Sometimes, depends on the app.  But a framework suffers from the single-point-of-failure problem.  It's worth an attacker's while to crack you, since it'll crack lots of stuff.

Hmm.. possibly one could write a macro in a framework header to hash some aspect of the framework in some way and check it against a known value.  That way the checking code would be embedded in the executable, and we'd at least be back to someone needing to patch each executable file in order to crack it.  That could protect against framework swapping (maybe), though not an InputManager.

----

The workaround for the InputManager hack is to just not support them in anyway, since 99% (all?) InputManager's are hacks anyways. Simply don't launch the app if an input manager is installed (hmm but I guess someone could could write an InputManager to override the function that checks that...).

*Hacks, yes, cracks, no.  I would not use your software if it refused to launch with any input managers installed.  Look at the ViStyleInputManager page currently on RecentChanges.  Is that even a hack?  Seems in keeping with the intended usage of input managers to me.*

Holy nuts. So you think that anybody who's using Taboo should not have the privilege of using your application? I smell a slight whiff of arrogance....

----

New person here, keep this in mind, last weekend I got so fed up with acquisition's nags which seem to increase every version that I went so far as to hack all of them out in three hours. This is from someone who almost never pirates software (I currently have no pirated software on my system) It was trivial to do with an input manager. Perhaps there is a way to check the input manager's identity WITH an input manager overriding the NSBundle loading machanism.

Who knows.

Just one thing to consider is not to tick people off with nags enough, or they will spend the time hacking out their system as I have.

*And why not pay for it if you find it useful enough to spend time hacking out nag screens?*

Heh - you're lecturing someone who uses Acquisition on whether or not they should register something? Man, talk about the wrong tree. ;-)

*Good point :)*

----

And don't forget, even if you restrict input managers (which will stop a lot of users from using completely legitimate extensions with your app), there's still APEs, mach_inject, OSAXes.... Mac OS X has no shortage of ways for users to get into your app and modify it while it's running.

While this is admittedly true, it is also true that InputManagers are the simplest way of injecting code into a Cocoa application.  All of the other methods require even more skill to implement. --OwenAnderson

Actually, in my experience, writing an APE is quite a bit easier than writing an input manager. APE's mechanism is available for free, is well-documented, has examples, etc. Input Managers are fairly poorly documented and are rather bizarrely laid out. But *none* of these methods are difficult to use for somebody who already understands how to crack the app once he gets inside it.

----

New person (making a living selling software): my advice is just make the software good, use a license key scheme which isn't brain dead and then release the program.

Let the �check for updates� feature or similar report if the program is registered or not. That way you can see if cracked binaries are actually a problem. I myself, and most of whom I speak with, ignore piracy because with a proper license key system it is not a problem.

Cracked binaries is not a serious problem, nor are input managers overriding functionality because really few people use these things (especially with all the trojans floating around with the cracks), and these people wouldn't pay for the software anyway.

Also, release updates with new exciting features regularly. Each time you release an update, all users using a cracked version of your program will need to wait for a new crack. And check out the crack-sites today, you need an invite etc. to actually get to these cracks, meaning, the people who hang out at these sites (and have access to cracks) are people who live and breath for �the crack scene�, and these are not your potential customers.

*I agree. We Mac shareware developers have to worry a lot less about piracy then commercial developers. Piracy on the Mac is much smaller then it is on Windows, simply because Windows has more marketshare. I wouldn't spend your entire week trying to get a super secure registration scheme. Just make something decent, and move on.*

I agree wholeheartedly.  As the author of AquaticPrime, my goal in releasing this framework/library is so that 'something decent' is available and easily usable for the masses.  This way developers don't have to waste time which could be spent bettering their application.  Here's a good musing on piracy and the futility of attempting perfect copy protection by Wil Shipley: http://wilshipley.com/blog/2005/06/piracy.html

*Yeah, okay, but "something decent" should mean not totally trivial.  I'd rather one idle google search not do the trick.*

**Read the first article linked to, it's trivial to implement and the only feasible way to crack the scheme is by doing a binary crack of the application, which is a negligible problem.**

I've read the article, and a swappable framework is still a problem!  My goodness..

**And as the paragraphs above say, you're worrying about nothing. Please point me toward an example where a program was actually cracked (as in, modified the binary, patched by an input manager, APE, a library was injected into its namespace, or similar), and where such a crack actually made a dent in the sales. The problem to worry about is fake/leaked serials -- at least this is my experience selling software, and others in the business agree with me, like the linked-to article by OmniGroup co-founder, also backed up by one of the guys behind Freshly Squeezed (see http://nslog.com/ ).**

----

I am currently questioning my theory behind this idea, because I do not believe that making it difficult for a pirate or a cracker will prevent them for attempting to. But I feel that if you prevent the most novice of pirates from cracking your app with small protection schemes, then you will atleast delay the cracking of your app. Most programmers worry most about sniffed serials, and it is actually quite easy to prevent serial sniffing and require serial generation to break your app. What I suggest is in your function for validating the serial, don't generate a valid serial based on the user input (name, email, etc) but instead decode the inputted serial and compare the decoded values to the user input. The only problem with this is you have to assure that serials are unique, so you have to either let it be a dynamic length or limiting length. Anyone have any good criticisms of this method? --ACoolie

----

I actually remember doing something like that for a small company I worked for. The key stored not only the features allowed, but a checksum, and to validate the key, that checksum was never generated in memory. Instead, the feature side was halfway made, the checksum was partially unmade, and then the half-keys were compared. The other problem was that the spec called for this to be a single library. So I made sure that any program that called it checked not only the reg key, but a few known bad keys, just to make sure the library was still checking.

I'm also reminded of an article in Gamasutra ( http://www.gamasutra.com/features/20011017/dodd_01.htm --Turn off javascript if the registration isn't working - yes, ironic, I know), but it's kind of overkill for shareware. As an aside, has anyone tried to offer a discount for upgrading from a cracked version? To appeal to their sense of getting something for cheaper. --Blain

----

One thing to be aware of when implementing any kind of validating (and this may be blindingly obvious, but I'll point it out anyway) is not to reduce all of your clever coding down to a single boolean test in one place. That represents a very easy point of attack since flipping the state of a bit is a lot easier than trying to crack any more complex scheme. I'd be interested to know what others' strategies are for avoiding this, but one solution I've used in the past is to make the test for validity a macro that wraps a complete check of the entire key, however that works. Then every place you need to perform the validity test, use the macro which expands into a complete test every time. A cracker has to patch every place the macro got expanded, not just one. Of course that assumes that your key checking is built right into the app, rather than in a callable library. Any time you reduce your validity test to a yes/no result from a function gives a cracker an easy point of access. Avoiding that can be harder than it looks because at some point it always does reduce to a yes/no result - is this app registered/unlocked or not? --GC

----
my strategy is to release everything as open source. just dont even bother. how are you going to implement a key scheme on an ipod?

----
One strategy I have seen (for an iPod product which was just a bunch of specially crafted notes files) was to have a custom installer and put the license checker in the installer.

----

now *that's* hack proof ;)

----
If your goal is "hack proof" then you're doing it wrong. Nothing will be hack proof. Your goal should be to keep honest people honest to the extent that you can without pouring too much effort into your protection.

