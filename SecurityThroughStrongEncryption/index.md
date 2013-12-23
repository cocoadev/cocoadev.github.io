---
layout: page
title: SecurityThroughStrongEncryption
---



*Use Of Strong Encryption Schemes -- see also AquaticPrime

**Take a look at this page describing the RSA cryptosystem: http://en.wikipedia.org/wiki/RSA **

From the first response to the MarcWeil comments in CocoaInsecurity:

*
Instead of trying to obfuscate your executable, why not just use mathematical strong encryption for the serial number? For example generate a public and private RSA key, embed the public one in the executable and encrypt the user name with the private one, when he buy a key (and do a base 16 encoding or similar to get an ASCII representation).

So the validation function would simply decrypt the serial number and compare it to the user name, if there is a match, then unlock the extra features -- it would still be easy to crack the program, but it would be very difficult to generate a new (valid) serial number, so the only option is to leak it (but an internal B**'lackList could remedy this).
 (B**'lackList could be a quickie topic, for example)

At least this is the scheme I would use, so please point out any weakness it may have --AllanOdgaard

*

The following discourse was initially in CocoaInsecurity, but evolved into a down-and-dirty discussion of encryption

----

One scheme would take the serial number and *decrypt* this using asymmetric encryption -- the decrypted serial number would be a record which would amongst other contain a hash for the user name, so even generating all the 18.446.744.073.709.551.616 possible fake keys (assuming a serial of 20 characters in base 16), and finding those which would decode to something valid, there would still be a further step of finding a user name which would map to the hash stored in this record -- so in other words, even if the cracker gets the actual Objective C source to the program in question, he would *not* be able to generate a fake serial number.

Secondly, distributing an InputManager as a crack, although small in size, is really not that easy. At least I know a lot of people who have easy access to Surfers Serials or Serial Box, but very few who know where to obtain a crack -- putting aside the fact that this only works if we have a class with a method that can be overloaded to return something else or set an ivar different (to indicate the program is registered) -- I think it is pretty easy to avoid such code.

I do understand that your original intent of this discussion was to focus on how easy one can look into an Objective C application (even without source), I am just trying to steer away from the "security through obscurity" and propose that we base our security on a theoretical secure model, rather than a practical difficult model.

The user has full control of the run-time environment (and so he should!), trying to circumvent this is IMHO naive -- similar to how it is naive to think that a computer to whom the user has physical access, can ever be secure.

--AllanOdgaard

----

Testify! People have been "cracking" copy protected desktop software since the Apple II. The notion that software licensing schemes can be "uncrackable" or that mach-o / Objective C makes Mac OS X "more vulnerable" is absurd. I personally cracked the copy protection on a CDRom game back in 1994 using nothing but ResEdit and Macsbug; not having Objective C didn't seem to be a problem. 

Even strong encryption is not foolproof. Given someone else's license file (i.e., a serial number and key for that number from a warez site) I should have no problem decrypting the object code. So, the people who want to cheat will still cheat, and the honest people will get screwed when they have a hard-disk failure, or otherwise lose their key. 

And now, the painful truth: there is no perfect copy protection/licensing scheme. The best you can do is discourage people from STEALING your software without discouraging people from USING your software. People administering computer-lab environments *HATE* elaborate locking schemes (strong encryption, CD keys, dongles), since they prevent homogenous system images (i.e., installing the same bits on every computer, even though each computer has a different MAC address, or whatever). If a lab director chooses not to buy 10,000 copies of your program because she can't roll it out automatically, then how much money are you saving? 

To summarize, copy-protection schemes are all well and good, but there's a limit to their efficacy. The more difficult you make a program to steal, the more difficult you make a program to use. And there will always be someone, somewhere, stealing your program.

-- MikeTrent

----

Re: Allan's most recent post, first paragraph:

A 20-character serial number seems unwieldy to me, but more importantly, to generate a fake serial number, you don't need to go through all those steps. You don't need to find ALL the 903#@2ljwer1&$912 fake keys. IIRC, with RSA, the public key can be used to decrypt any signature (serial number) to a hash value. Maybe there are hash values that no source message (user name) can produce, but I'll bet that if you try a few random signatures, you can find a hash value for which you can also find one valid source message.

This is a fundamentally different problem than what private/public key signing is designed for. It is normally used so that somebody else can't type a message and sign it as you. Following this analogy, a person generating a fake serial number isn't trying to sign a specific message as you....they just want any valid message/signature pair.

-- TravM

----

I probably wanted to have said 16 characters and in base 32. So that gives us a serial number of the form ABCD-EFGH-IJKL-MNOP, which I think is pretty standard. And the base 32 makes it 16x6/8 bytes = 12 bytes (when decoded).

I was not talking about having the serial number be a signature for the user name, but rather be a record (of 12 bytes) which would (amongst other) embed a signature for the real name -- so there would be a 1:1 mapping between the possible 12 byte records (where only a few would constitute valid data) and the possible serial numbers (where 256^12 exists), so the hacker would not be able to systematically generate serial numbers that would map to a valid record, but would need to randomly generate them, decrypt them and see if the decrypted version would be accepted by the program, which would amongst other mean that the user name which goes together with the record, would need to hash out to the signature stored in the record (and I agree that this is not a hard problem, depending on the message digest algorithm used), but it would be the second problem to solve, the first one is the difficult one.

--AllanOdgaard

----

Ah, I like this idea. Maybe this is an established method, but it's new to me. Let's see if I understand it with an example implementation.

First, to be precise, 32 is 2^5, not 2^6, so we are talking about a 10-byte serial number. But let's make it a 12-byte serial number by allowing case distinction and a couple of symbolic characters.

Say we want 3 bytes of space (256^3 ~ 16 million values) for our user name hash value. There will be unused and repeated hash values, but it seems reasonable to choose a hash space comparable to the number of expected registered users(!). As we'll see later, we want to minimize repeated hash values. The hash value takes up 3 bytes, which leaves 9 bytes for a random fixed value, F. We generate F once, probably when we are generating our public/private keys. It is just a random number, unrelated to anything, and it is public knowledge. It is distributed with the application along with the public key. To generate a serial number for a user name, concatenate F with the hashed user name, then encrypt the result with the private key. When the user enters the serial number, it will be decrypted with the public key and the first 9 bytes must match F. The last 3 bytes must match the hashed version of the user name.

The problem of finding a method to generate a serial number that will decrypt to match any given F is equivalent to deriving the private key from the public key, which is a hard problem. But given a specific F value, the best way to find a good serial number is to search for it brute force, and only 1 in 256^9 values will work.

One flaw is that someone already in possesion of a valid serial number can probably find several user names that hash to the coded value. You'd have to blacklist that serial number. If you made your hash space large enough, it's unlikely that anyone other than the leaker will be affected by the blacklist (by having the same serial number).

Also, with RSA, to make a 12-byte encrypted serial number, the largest key you can use is 12 bytes (96 bits), which isn't considered especially secure. Do other methods allow a larger key? Or is there a way to do it with RSA?

And does anyone see other flaws?

-- TravM

----

http://developer.apple.com/security/index.html

also: http://developer.apple.com/samplecode/Sample_Code/Security/CryptoSample.htm

----

Just to follow up on the points made by TravM -- different user names for the same serial is not really a problem, as I'd blacklist the leaked serial, not the name. The point about 96 bits being insecure is also a concern that I have had however, insecure defined by RSA Labs means that it can be broken by a distributed effort in a few months by several thousand computers. Also, alone the fact that generating a fake serial is a mathematical challenge (instead of just a debugging task) would probably put off most crackers --  as a last resort I guess I could abandon the serial number and just E-mail the user a 1024 bit key in base64, which he would paste into my program.

--AllanOdgaard

----

If anyone cares, the above was written a couple of years ago IIRC, and today I actually do use RSA (1024 bit) for my product, which has been out for approximately 9 months, has a (registered) user base in the four digits (and growing :) ), and I've had no problems with fake/leaked serials, nor has the handful of cracks (for older versions) posed any actual problem.

That cracks is not a problem is probably a mixture of a) releasing new versions often, b) they are actually not very easy to obtain (you need invites to the various cracking sites to get them), and c) I have passionate users.

--AllanOdgaard

* There is also a lengthy discussion of the relation between security systems and registration systems at MakingSecureRegistrationCodes

