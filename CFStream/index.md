---
layout: page
title: CFStream
---

CFStream works with sockets, memory, and files. It is documented at file:///Developer/ADC%20Reference%20Library/documentation/Networking/Conceptual/CFNetwork/index.html#//apple_ref/doc/uid/TP30001132

See also CFNetwork.

----

Some notes on the functions:

*CFReadStreamRead - Use this to read up to a certain number of bytes. Only blocks if no bytes are available, but does not block until all bytes are read.
*CFReadStreamGetBuffer - Use this to read all available bytes. Only blocks if no bytes are available, but does not block until all bytes are read.


Really, CFReadStreamRead and CFReadStreamGetBuffer are pretty much interchangable, except that not all streams support CFReadStreamGetBuffer.

