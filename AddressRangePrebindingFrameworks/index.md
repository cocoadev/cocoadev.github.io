---
layout: page
title: AddressRangePrebindingFrameworks
---

Does anyone know how to find out what the build address range is for a framework so I can space mutiple frameworks accordingly when I prebind and specify the segments with the flag "seg1addr".

--zootbobbalu

----
Maybe this is what you're looking for: http://developer.apple.com/documentation/Performance/Conceptual/LaunchTime/Tasks/Prebinding.html#//apple_ref/doc/uid/20001858/102177
--HuaYingLing

----
I was under the impression that otool can give you some info. I would have to check again...

