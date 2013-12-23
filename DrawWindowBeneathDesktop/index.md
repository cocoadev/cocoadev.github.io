---
layout: page
title: DrawWindowBeneathDesktop
---

I'm attempting to draw a window beneath the icons on the desktop (above the wallpaper). The http://www.cocoadev.com/index.pl?NSWindowLevel page lists the constant that will draw a window beneath the desktop icons (kCGDesktopWindowLevel), but, as noted, windows created with this constant will still accept mouse down events even when clicking on an icon. Ie: you will be unable to select your desktop icons that happen to be located above one of these windows. Any idea what I need to do to get the result I'm after?

Essentially I'm after the appearance of Stevenf's WebDesktop application (without the whole "web browser on the desktop thing").

Update: I've stumbled across this header file:

Macintosh HD:System:Library:Frameworks:ApplicationServices.framework:Versions:A:Frameworks:CoreGraphics.framework:Versions:A:Headers:CGWindowLevel.h

that defines more window level constants, but none of them accomplish what I'm after... anyone have something that would point me in the right direction?

-- Dave

----

use

<code>[yourWindowOutlet setLevel:(-2147483628)+1];</code>

to do what you want.

(-2147483628) is the undocumented level for desktop window (in wich wallpaper is drawn).
Adding 1 to be sure to be over desktop window (wallpaper) but under other windows (including Finder's icons wich are "windows" too in WindowServer with an higher level).

-- Bru

----

Be careful when doing this - setting windows to this level will cause them to become "sticky" when using virtual desktop tools like VirtueDesktops and Desktop Manager.

--TonyArnold

----

An easy way to discover any window's level is to use Quartz Debug's -> Show Window List. You should notice a column titled "Level" towards the right of the table. Of course you'll need to install CHUD first..

--Mike

