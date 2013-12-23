---
layout: page
title: ProtectingResources
---



I'm working on an app that contains within its Resources folder some very sensitive information.  I've been looking into ways to prevent people from poke around with it by exploring the package with little or no success.  I am not aware of anyway to block access to the file itself, so I have been looking for methods of encrypting it.  Anyone got any suggestions on what to  use? Cocoa/ObjC things are best, but I can use plain C if necessary. --OwenAnderson

----

How large are your resources?

Unlikely to ever become more than a megabyte.  Also, I suppose I exaggerated when I said "very sensitive".  It's not like people's credit card numbers or something.  But it is people's intellectual property and I don't want just anyone to be able to easily extract it from the package. --OwenAnderson

----

If you want the application to be able to read the resources w/o user intervention, then no encryption method will be foolproof -- people will be able to figure out how the app decrypts the data. Of course, that's not "easy" ;-). Anyway, you're probably talking about some kind of symmetric compression mechanism -- essentially some way to scramble and unscramble the data.

If the information is user data, such as documents or the like (what would they be doing in Resources?) you could work with OpenSSL to encrypt and decrypt the data using a user-supplied public/private key pair. The user would need to supply the key, of course. 

Or, for what it's worth, the Security framework may be of some assistance here. Consider that the Security framework has mechanisms for encryption, decryption, storing data in user keychains, etc. 

-- MikeTrent

----

I looked through the Security framework but couldn't decipher the API, and the documentation was little help.  Does it implement such symmetric ciphers?  If so, where's the documentation on them?  I looked into crypt(), but I could never find any good documentation beyond the man page, which is just a tad spartan for someone without a background in cryptography.  --OwenAnderson

----

crypt() is only useful for a one-way encryption. It was designed to encrypt user passwords. Rather than comparing your plain-text password to a decrypted version of the password stored in the passwd file, unix systems use crypt() to hash your plain-text password and compare that hash to the version on file. This way the system can minimize (if not eliminate) the risk someone will get hold of your plain-text password. This approach is also used by password-cracking programs: a dictionary of words is passed to crypt one at a time, and the resulting value is grepped against the passwd file. To my knowledge, there is no way to go from a crypt() value to the original source string.

I agree the Security framework API is a mess, but it's not as bad as IOBluetooth. I'll take a look at it. Maybe someone else here has some advice on its use.

-- MikeTrent

----

You know ... if you don't *care* how the data is processed, and you just want to discourage people from getting at the data w/o actually making any kind of guarantee about encryption, you could just pass your data through an exclusive-or stream. XOR is great because it's fast, reversable, and produces random looking results regardless of input. This similar to what DES encryption is doing; except DES also involves a sophisticated generating function.

See CheesyXOREncryption for an example of what I'm talking about.

Or just get some DES source. You can find algorithms on the web. I found this site interesting: http://www.ch280.thinkquest.hostcenter.ch/crypto/dese.html -- maybe others will tickle your fancy.

-- MikeTrent

----

I would think that openSSL comes with DES -- though IMHO DES is really security through obscurity, does dozens of weird eors, bit swaps a.s.o. without any real reasoning -- I much prefer TEA, a Tiny Encryption Algorithm (for simple stuff).

http://www.ftp.cl.cam.ac.uk/ftp/papers/djw-rmn/djw-rmn-tea.html

----

What would be the best solution for encrypting large plain-text files? The speed is most important.

M.R.

----

First of all, start by compressing them using zlib or similar. Then any symmetric block cipher encryption scheme should be just fine, i.e. DES, AES, TEA or similar... perhaps even just the XOR-trick Mike outlined. It really depends on how critical performance is versus security -- however, having the files compressed first should add to the security, as it will be harder to find patterns in the text (but of course, compression probably takes longer than most of the encryption schemes).

----

"Does dozens of weird eors, bit swaps a.s.o. without any real reasoning" seems like a good description of TEA IMO ;-) But I'm no cryptographer...

W.R.T. the fastest way to encrypt a plain text file: XOR. Since XOR is basically the same as assignment I'll wager nothing will be faster. Use the function above, but don't bother with the srandom calls and replace "scrambler = random();" with "scrambler = key". Very fast, although not terribly secure -- I'm sure a real cryptographer could break the file in short order. Using zlib to compress your data will just make your algorithm slower, and since speed is most important, forget it. Using DES will probably be slower than XOR. Using TEA will be slower than XOR. Using public key will be waaay slower than XOR. Heck! XOR! Woo!

I think the zlib trick is cool, especially when combined with XOR in some way for a poor-man's scrambler, but again, it's just obscuring the source. I agree, for real applications use OpenSSL or SecurityFramework. But Owen's original problem was he didn't "want just anyone to be able to easily extract" his data, not encrypting sensitive data. I call this level of security SeatLeashSecurity, to borrow a metaphor from the bicycle world.

-- MikeTrent

----

Also, I think that for protecting intellectual property like Owen wants to the encryption doesn't really have to be insanely uncrackable.  It really only matters that it would take some work to get in. To the best of my knowledge, the DigitalMillenniumCopyrightAct (DMCA) explicitly prohibits "circumvention" of any copyright protection schemes, no matter how flimsy they are.  Just look at the big deal made over DVD scrambling and programs that can rip DVDs to easily sharable mpegs.  DVDs are hardly unbreakable, but they hold a work that took millions of dollars and a lot of people to produce.  Just the fact that there is a barrier there that must be broken is why the DMCA protects DVDs. Owen's issue seems like a similar problem.  SeatLeashSecurity would probably be good enough.  Just my 2 cents.

-- BrianMoore

----

Well, if you do not want people to know the contents of the data, you should use strong encryption rather than fall back on laws -- I think I should be able to do whatever I want with the products I buy, as long as I do not break real laws, like selling copies of copyrighted material -- but to make an mpeg out of a paid DVD, so that I can bring it with me on my cellphone or reverse engineer a bought program to change an irritating behaviour is IMHO fully within my right as a consumer, if you don't want me to tamper with your product, don't sell it to me in the first place! -- but then again, I don't live in the land of the free...

-- AllanOdgaard

----

The problem really is that the product I'm working on is a tool for content distributors (albeit a content type that doesn't have a HUGE I.P. investment involved, unlike, say, a movie).  Thus whether or not I agree with it, I need to provide at least some form of deterrent to stealing the content.  Keeping honest people honest.  Just like with the iTunes Music Store you can theoretically make infinite copies and sell them, but they try to deter it with limited restrictions. --OwenAnderson

----

I wrote a Cocoa wrapper class for OpenSSL encryption and decryption and checksum functions. It's really easy to use and can encrypt/decrypt using any of the ciphers that OpenSSL has available (a lot). You can learn more about it here:

http://septicus.com/products/opensource/

-- Ed Silva

----

One thing I'd like to know: given that you have encrypted files your app needs to access, this implies that 1) you must call a routine to decrypt it and 2) you must supply a key for the decryption. But how do you hide the key in your app?

----
Another thing you could do is embed the data in the binary itself. That would get it out of plain-sight, if nothing else.
----
From a theoretical standpoint, you cannot hide the key. Without hardware support a la TCPA, if your app can decrypt the files, your user can decrypt them as well.

From a practical standpoint, you can do a lot of obfuscation in the same way that people obfuscate their registration systems, and then hope that nobody is motivated enough to break it.

*Yes, see above, I call this level of security SeatLeashSecurity. -- MikeTrent*

Is this really the only way? As I recall, the way CSS was discovered is because the developer of one of the players forgot to encrypt the key (http://wired-vig.wired.com/news/technology/0,1282,32263,00.html). But I guess encrypting the encryption key means you need the unencrypted key for that somewhere (chicken and egg problem).

Without hardware support, yes, it is the only way. If a program uses its resources, it must decrypt them. Decrypting them, they must live in either memory or on disk, or both. 

Now, take a look at     /dev/mem and     /dev/disk* and ponder this for a little bit. *That tells me absolutely nothting.*

    /dev/mem gives you access to all of the contents of your computer's memory.     /dev/disk* gives you the all of the contents of every disk attached to your computer. Since the unencrypted data must be present in at least one of those while the app is using it, it's possible to use those devices to extract it, no matter how obfuscated you make it. In the end, the user owns and controls his computer entirely.

*Hmmm, one has to wonder why the **AA et al put so much resources into hiding their stuff if it ends up being so easily accessible in the end.*

No, one doesn't have to wonder. They put as much effort in as they feel they need to. Sometimes I lock my car even though I know a seasoned criminal could drive off with it in 2 minutes or less. Hell, I locked my keys in my car not long ago and used a friend's key no problem. Different manufacturers, make/model, whatever...his key unlocked my door, thankfully. With it being so accessible in the end why do I go through the trouble?

----

Sorry for going a little off-topic (and perhaps write what some would consider flame bait) but as for the **AA and the resources having to be in memory, this is what the new TrustedComputing tries to remedy, and according to news, the x86 developer machines from Apple uses Intels Infinion chip.

To give a quick idea of what this is: it's a chip which can monitor the machine, that is, it can verify the state of the machine (i.e. that the machine only has authorized hardware, and is in a known good state (i.e. an unmodified version of OS X has been booted)). It contains cryptographic keys which it will only allow to be used when in a known-good state, and by a known-good program. It also has its own memory which is shielded from other applications.

So what this means is that e.g. iTunes can be certified, and only iTunes will be able to decrypt received data (from the ITMS) and will do this in the shielded memory, using keys only available on the co-processor. If you patch iTunes, it's no longer certified, and thus will not be able to decrypt the music/movies you've bought.

So while DRM is theoretically impossible today, it may require hardware hacking in the future. So **AA are probably not wasting their time/money, if they have helped the industry (including Apple) to switch to TrustedComputing.

