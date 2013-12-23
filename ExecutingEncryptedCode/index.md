---
layout: page
title: ExecutingEncryptedCode
---



I'm trying to work out how to execute encrypted code on the fly. Here's the basic idea:

At build time:

1. Build the code. Stick it in an NSBundle (or whatever).

2. Encrypt the actual binary.

3. Sign the binary. Resulting file is cyphertext + signature.

At run time:

4. Read the cyphertext + signature into memory.

5. Verify the signature.

6. Decrypt it.

7. Execute it, or somehow make it available for execution.

I know how to do all of these steps except the last one. If I have a blob of binary data in memory? How do I execute it (or make it available for execution)? Ideally, I'd like to neatly package this in an elegant NSBundle subclass that hid all the nasty details away inside the -principalClass and/or -load methods. Ideas?

----

*Here's a tip along a path you may not have considered: Don't bother. **NOTHING** you do to your code to protect it will be unbreakable. All anybody has to do in this particular case is look at the bundle to get the key and they can decrypt it themselves. If you're doing this for your own interest, great. If you're doing this for a shipping product and are actually planning to rely on it to protect your code, it's a complete waste of your time.*

That's not the kind of advice I as looking for. I'm not looking to "protect" the code. The encryption is just to deter casual snooping; I know I can't stop people from decrypting it. The important part is the signing; I want to **verify** that what I am about to run is what I think I'm about to run. That's my main goal. As I said, I have no problem figuring out how to do any of this; I just want help with step "7", how to execute a blob of binary data that's in memory, or more accurately, how to get the runtime to see it.

----

How hackish do you want to be? You can cast memory to a function pointer if you know the signature - but bear in mind this approach (to say nothing of the code!) may break on different platforms, as security levels differ.

----
That's not really helpful unless the loaded code is self-contained, which is highly unlikely. If it links against external frameworks or dylibs, which is almost certain, then dyld has to get involved to hook everything up at load time. So the real question is, how do you get dyld to effectively "load" a chunk of code that's already in memory?

The answer is (caveat: I haven't done it, I'm just reading man pages) to use     NSCreateObjectFileImageFromMemory (in     man NSObjectFileImage) to create a     NSObjectFileImage for your code. From there, use     NSLinkModule (in     man NSModule) to actually link it. Once it's linked into your process, you can use     NSLookupSymbolInModule (in the same man page) to get a pointer to a symbol. If you're looking to duplicate NSBundle's     principalClass, then you'd parse the Info.plist, get the name of the class, construct the appropriate symbol name from it, and get a pointer using this function. NSBundle also has the ability to return the first class in the image if nothing is defined in the Info.plist, but I have no idea how to do that, and maybe you don't care. I hope this can get you started.

----
Brilliant. Will see how far I can get with those NS routines. Thanks for the tip off.

----

Check out NSCreateObjectFileImageFromMemory. I don't think you can load ObjectiveC based code, but you can load code that links against CoreFoundation.

http://developer.apple.com/documentation/DeveloperTools/Reference/MachOReference/index.html

http://developer.apple.com/samplecode/MemoryBasedBundle/listing3.html

--zootbobbalu

----
Many thanks, especially for the link to the MemoryBasedBundle sample code.

