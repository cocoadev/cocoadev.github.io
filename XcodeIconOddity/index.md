---
layout: page
title: XcodeIconOddity
---

I created a 128x128 icon and gave it alpha in Photoshop and dragged into Icon Composer and bg is all greyed out indicating alpha working so I add the .icns file to my project and specify it to be the icon for my app.  Launches fine and is properly transparent in dock, but not in finder - white block in finder.  Is there some trick?  I selected what I wanted of image and made a new channel and it seems right - and works right in Dock as I said.

Help? Thanks.


blakespot

----

Open up the icon again and confirm that the mask is set properly for **every** icon size. If the size of the icon in the dock is different than the one in the finder, it might be using a different representation of the image. Play around with changing the size of the icon in the finder/dock to see if that's the problem. -- General/RyanBates

----

...hmmm.  In the mask for the other sizes - it's just black for the clickable area, white for the non-clickable bgnd, right?  Or is it more complex than that?   Thanks.


blakespot

----

No, you are doing it correctly. It uses a 1 bit mask (only two colors) for all of the icons smaller than 128x128 pixels. I don't know what the problem could be if this is set for each of the images. Instead of using Icon Composer, look at it in Icon Browser, it shows a few more representations. Not all of them need to have an image, but each image should have a corresponding mask. You could also try remaking the .icns file. Did changing the size of the icon in the finder/dock make a difference? -- General/RyanBates

----

Here is a screenshot of Icon Composer and Icon Browser running, both looking at this particular .icns file.  Do I look to be in trouble here??  Thanks.

http://www.blakespot.com/images/icons.jpg


blakespot

----

Ahha, notice the 8 bit mask is all black. I have no doubt that's your problem. Try remaking the .icns file and see if it has the same problem. Are you importing a tif file made in Photoshop or something? -- General/RyanBates

----

...but I don't see how to "remake" the file or do anything to the 8 bit mask.  Icon Composer does not let you edit the 8 bit mask.  ??  

Thanks  --blakespot

----

What images are you loading into Icon Composer? That may be affecting the 8 bit mask. When I created an icon using Icon Composer, the 8 bit mask was correct. I used flattened TIFF images with a single alpha channel (created in Photoshop). -- General/RyanBates

----

I think I did a cut and paste from Photoshop.  The orig had a single alpha, for the 128x128.  I have since removed all but the 128x128 and the icon behaves fine.  Is this a problem?  Why must I have the smaller images at all?

Thanks -- blakespot

----

If they aren't specified, I'm guessing the smaller representations are created automatically by either Icon Composer or the Finder. I can't imagine the Finder resizing the large representation every time it wants to display the small icon. Anyone want to confirm this? -- General/RyanBates

----

Not sure about that, but I can confim that you can't just paste from Photoshop into Icon Composer and get a proper 8-bit alpha channel in your icon.  That will result in a black alpha channel exactly as shown in the image above.  I had to write out a .tiff file with an alpha channel and then read that into Icon Composer to get a proper alpha channel in an icon. -endymion

---- 

Agreed on the copy/paste. Configure the alpha/transparency, then save the files out to .png files, then use them from disk. Copy/paste = baggage :)
