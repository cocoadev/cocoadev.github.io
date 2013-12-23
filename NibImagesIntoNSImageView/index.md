---
layout: page
title: NibImagesIntoNSImageView
---

I have a number of images in my Nib file and I dragged an NSImageView object from the Controls panel
into my dialog. I want to set each image into the same NSImageView such that each image lays on top of
the previous producing something like a totem pole when finished. I use setImage: but I don't
see how yet to position each image and still show all the images. Maybe NSImageView is not the way to go??

----

NSImageView takes only one image. Either combine all your images the way you want them to look into one single image or drag multiple image views and set each one individually.

