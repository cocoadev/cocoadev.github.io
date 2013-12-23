---
layout: page
title: HowToMakeAquaIcons
---

How do YOU make those beautiful icons for your latest application?  Tell us here...

----

First of all, you should read Apple's Aqua Human Interface Guidelines. Not only does it explain how a well-designed OS X icon should look, but it also explains how a well-designed OS X app should look overall. See http://developer.apple.com/ue/aqua/icons.html . A good program for "aquefying" graphics is AquaTint- see http://www.sticksoftware.com/software/Aquatint.html . You simply input a monochrome image, and it aquefies it. Remember, don't overuse the aqua look- only use it where appropriate (UI widgets, etc.).

-- AdamAtlas

----

Remember, Aqua icons should not just look glossy and pretty, they must send a message to the user. Sometimes it is hard to create icons from scratch so take a look at someone else's icons for inspiration and develope a new concept from the basic idea. However, one must be careful not to create ambiguous icons. For example, an icon for "Connect" could be easily mistaken for "Plug-in", if someone was to make an icon of a cable being inserted into an electric outlet. In cases like that the designer should use 'default' icons idea like the ones seen throughout Apple's applications. For the "Connect" icon above, you could have many options. Apple uses a Telephone icon in Internet Connection app for example.

Apple's interface icon sizes are:

128 x 128 pixels - Application Icon - .ICNS

32 x 32 - Toolbar Item Icon - .TIFF, .PNG and .ICNS recommended, Apple uses .TIFF mostly

48 x 48, 32 x 32, 16 x 16 - Finder List / Column Views Preview Icons - Included in .ICNS for Application Icon

How do Finder Icons work?
Finder (and all OS X windows with the ability to browse file systems) gets Application Icons in format ICNS and reads different "pages" of the ICNS format to find icons in different sizes. When creating an ICNS file with Icon Composer (Developer Tools), it will ask for four different icon sizes. It is highly recommended that after creating the main 128 x 128 file, you _redesign_ the icon for the smaller sizes, otherwise they will lose quality and might be hard to identify.

Software mainly used for creating icons are Adobe Photoshop, Adobe Illustrator, Macromedia Fireworks and GIMP. You can also use additional software, but those should do the job. Pick one or two that will complement each other's job and get to it!

-- FernandoLucasSantos

----

Where did those generic Aqua icons go?  I tend to need them from time to time but can never locate them.  Can anyone provide a working link or perhaps a zipped copy? -G

----

Does anyone know where I can find a .png of the generic (blank white paper) OS X document icon?  I can modify it into my own document icon, but first I have to find it.  (Screen-capturing it from the Get Info dialog doesn't work, because it doesn't get the transparency.)  Thanks!  --DarelRex@gmail.com
----

What I did was ( so far as I can remember ):


*
in terminal

cd ~

touch newFile 

in finder - in icon mode - set icons showing at 128 pixels

screen-dump

open in photoshop or graphic converter or the like

remove everything outside the doc image

create a new shadow <- if do want more is better, don't do this now.

save it as png


----
The above is probably not such a good idea as you lose any alpha mask that was on the icon, looking for the .icns file is the best way to do things.
----

and if you want 'more is better'.

*
you can cut off the upper right corner ( curl ) into a new layer

remove the shadows and the rest of the curl

draw transparent shadows ( because otherwise your center ( app image f.e ) will be obscured by the old shadow )


*
copy a left part of the base image to the right side of the image. In a way it will become a equal rectangle document.

draw the transparent shadow


show the curl above that

select the base

cut off the part what is to much, the top right part of the doc base. I did move the cut selection one pixel to the left and one down. But check yourself what you like.

draw a grey square #5e5e5e with a size 56 * 56 into the middle. Drop shadows right from the top 50 or 60% transparency and tiny 2 or 3 pixels, also for the base image but 3 or 4 pix

*
The text was Lucida grande bold 18pix, only i don't remember if it was crispy or just strong. Crispy looks more original if I remember correctly.

The text was not in the middle under the appl box, just a little more down. Make the text centered in the icon and as text.
*
As last move the curl as the upper top layer of your layers, because of his shadows will overlap the box


Of course keep another around to compare

HTH
R

----
Or you can just find the icon in this folder: /System/Library/CoreServices/CoreTypes.bundle/Contents/Resources/
----
Maybe he can use folder icon x. Never used really, but I saw they have templates. But still, there will be the shadow problem of the curl if the app image is in the middle or to big. BTW. He mentioned that he was searching for a png. 
----
It's easy enough to get a png from an icns. Just open in Icon Composer, export the size you want (it exports as a tiff), then convert the tiff to png (which you can even do in Preview). Or, if you don't have Xcode Tools, you can use Iconographer to open the icns. That way you can be sure you've really got the original.
----
Dont forget in Leopard the largest icon size is now 512 x 512.

