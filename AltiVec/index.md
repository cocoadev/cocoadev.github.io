---
layout: page
title: AltiVec
---

AltiVec: a vector-processing unit added by Motorola to their GeeFour processors to compensate for the massive MHz gap between their chips and Intel-based ones. Allegedly responsible for their glacial improvement of the GeeFour ever since.

See http://episteme.arstechnica.com/eve/ubb.x/a/tpc/f/8300945231/m/8790959504 for a long thread by knowledgeable programmers on how to code decent AltiVec (apparently gcc is not very impressive at it).

You may want to give it a miss, however, and learn MMX/SSE instead.

----

Of course gcc isn't good at it, because it can't really know what you're trying to do. Don't forget about the Accelerate framework for the appropriate tasks (because it's supported on both x86 and PPC architectures):

http://developer.apple.com/documentation/MacOSX/Conceptual/universal_binary/universal_binary_vector/chapter_6_section_2.html

