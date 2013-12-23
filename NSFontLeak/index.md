---
layout: page
title: NSFontLeak
---

Is there a memory leak in NSFont?

At [http://216.239.41.104/search?q=cache:tL1hIV8YU-oJ:www.stevesloan.sjsu.edu/tech/pdf/10_2_6_leak.pdf+nsfont+leak&hl=en&ie=UTF-8] (Google HTML link to a PDF) someone said:


*IIRC, there is a HUGE memory leak in NSFont regarding font names. If

the font name does not match the PS name of the font (which has no

spaces) then NSFont will leak. So having "Lucida Grande" as a font will

cause a memory leak as it does not match "LucidaGrande". This can be

the cause of freaky huge memory leaks in Safari. Although it seems a lot

of things cache NSFont instances so they'll only leak once for each text

field*

I have a font viewer-type project and I'm seeing my memory usage (as reported by top) grow by ~5 megs and more, when all I'm doing is changing the font of a 10 character string in a text view. Does anyone know if the leak reported above ever existed? And if so, has it been fixed by now? I've been playing with this project off & on for over a month and I can't find any leaks in my code (all 80 lines of it.)

----

Run your app for a bit, then find its pid by using top or Activity Monitor, then open Terminal and type     leaks <pid>. It will show you all of the leaked memory in your app, what type of object it is, etc. AppKit does have a few leaks when it starts up. It's not that important, because it's a one-time thing, so don't worry if you have a few. If you have a lot of repeated ones, then start to worry. There are options you can enable to find out exactly where a leaked object was allocated and other useful things, too.

