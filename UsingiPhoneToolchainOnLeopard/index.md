---
layout: page
title: UsingiPhoneToolchainOnLeopard
---

Leopard is out, so you if you have successfully compiled the toolchain on leopard, you are encouraged to share your knowledge.

----


You can follow the tiger instructions to build toolchain with the following additions:

Before doing a make odcctools type:
export INCPRIVEXT="-isysroot /Developer/General/SDKs/MacOSX10.4u.sdk"

Then before you configure gcc type:
mv llvm-gcc-4.0-iphone/configure llvm-gcc-4.0-iphone/configure.old
sed 's/^FLAGS_FOR_TARGET=$/FLAGS_FOR_TARGET=${FLAGS_FOR_TARGET-}/g' \ llvm-gcc-4.0-iphone/configure.old > llvm-gcc-4.0-iphone/configure


All of these methods are outlined in the iPhone tool chain bug report sections. I think Erica has made a complete guide if someone can find a post a link to it.

Then whenever you need to compile something, modify your makefile so it add the -mmacosx-version-min=10.1
Sample makefile modified below and taken from the iphone wiki sample hello world application.
    
CC=arm-apple-darwin-gcc -mmacosx-version-min=10.1
LD=$(CC)
LDFLAGS=-lobjc -framework General/CoreFoundation -framework Foundation -framework General/UIKit -framework General/LayerKit

all:    Hello

Hello:  hello.o General/HelloApplication.o
	$(LD) $(LDFLAGS) -o $@ $^

%.o:    %.m
	$(CC) -c  $(CFLAGS) $(CPPFLAGS) $< -o $@

clean:




I know some folks have gotten the toolchain to make in leopard - but has anyone actually compiled anything with it?
