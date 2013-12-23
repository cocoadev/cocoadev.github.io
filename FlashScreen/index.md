---
layout: page
title: FlashScreen
---

Does anyone know of a way to flash the screen without having the Universal Access option "Flash the screen when an alert sound occurs" turned on?  I would really like to flash the screen without playing a sound too.  So, turning on "Flash the screen when an alert sound occurs" and using NSBeep(); is out of the question.  The only thing I have found that even mentions flashing the screen is Technical Note N2102 http://developer.apple.com/technotes/tn2002/tn2102.html it refers to the <OSServices/SystemSound.h> header, but that just looks like APIs for playing sounds there is nothing that indicates any of it would flash the screen.  Any ideas?

----

You could alter the screen's gamma or create a partially transparent white window over the whole screen.

----

Just do this:

    
FlashMenuBar(0);
FlashMenuBar(0);


----

Wow.  Perfect, F**'lashMenuBar(); is exactly what I was looking for.  Thank you.

----

Looks like the method is deprecated on Snow Leopard, alternatives?

