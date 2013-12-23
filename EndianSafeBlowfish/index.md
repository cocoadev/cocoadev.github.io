---
layout: page
title: EndianSafeBlowfish
---

Does anyone know how to edit a Blowfish implementation (MulleCipher?) so it is "endian safe"? i.e., at the moment I am not able to open an encrypted file created from PPC on x86. I know I need to swap big endian for little endian somewhere, but I have no idea where to start. Help!

*I would recommend that you start by contacting the author.*

But thanks for creating a page with a wonderfully-whimsical title. I want one of those in my local public aquarium!  ;^D

----
Look into libkern/OSByteOrder.h for functions that will properly handle endianess automatically for the compiled platform. --Seb

