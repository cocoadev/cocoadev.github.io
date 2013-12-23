---
layout: page
title: CFTypeRef
---

CFTypeRefs are CoreFoundation types, such as a CFDictionaryRef or CFStringRef. They are usually (always?) pointers to opaque structures, meaning you cannot dereference CFTypeRefs directly. Many CFTypeRefs can be exchanged for NSObject subclasses via TollFreeBridging.

