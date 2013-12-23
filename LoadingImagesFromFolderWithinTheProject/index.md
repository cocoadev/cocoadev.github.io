---
layout: page
title: LoadingImagesFromFolderWithinTheProject
---

Describe LoadingImagesFromFolderWithinTheProject here.

How I can load images from a folder within my Project? The folder is included in my project and copied to the target! When I added the folder to my project I used the following settings:

Copy items into destination group's folder (if needed):YES

Reference Typ: Relative to Enclosing Group

Text Encoding: Western (Mac OS Roman)

Create Folder References for any added folders:YES

For now I use a folder outside the project, but that works only for my Computer. Because the app cant find the images when I use the following code on another Computer:

    
myImage = [[NSImage alloc] initWithContentsOfFile:[NSString stringWithFormat:@"/Users/ludwigvilliger/Desktop/TrainerBilder/Bild%@.png", index]];


So I thougt, that would be a solution to add the folder to the target and load the images from there. But how?

----

I believe the recommended solution is to simply have the image copied into the resources folder and then to use +[NSImage imageNamed:@"yourimage"]. --AndyMatuschak

----

Yes, this exact the same what I have found out too! Thank you, indeed! If you are interested to test my just finished app, go to: http://homepage.mac.com/gugulino1/. It is a app for learning the names of chemical structures. When you dont know the name, then type six times something in the textField and the solution appears! Please let me know, what you think about! Thank you all for helping build this app� This Forum is really nice!

