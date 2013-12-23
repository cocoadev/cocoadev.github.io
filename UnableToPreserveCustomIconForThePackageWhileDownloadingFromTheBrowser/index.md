---
layout: page
title: UnableToPreserveCustomIconForThePackageWhileDownloadingFromTheBrowser
---

Hi,
 I had a requirement that I need to ship my application in the form of a package(.pkg). And also I need to set a custom icon for the created package. Creation of package and setting custom icon is working fine.

 But the problem is when I downloaded the Package from the browser(Safari,Firefox), its getting the default icon again. i.e whatever I have set the custom icon to the package, its not preserving.

 Here is the step by step, how I have set the custom icon.
 a) Build the source code, then got General/SampleApplication.app
 b) then using "General/PackageMaker", I have created General/SampleApplication.pkg.
 c) To set a custom icon, icon.jpg to my General/SampleApplication.pkg ,
                 i) use "sips" command�  i.e sips -i icon.jpg
                 ii) use "General/DeRez" command�  i.e General/DeRez -only ins icon.jpg > newicon.rsrc
                 iii) use "Rez" command�. i.e Rez -append newicon.rsrc -o General/SampleApplication.pkg
                 iv) use "General/SetFile" command�. i.e General/SetFile -a C General/SampleApplication.pkg


Till this point, everything is working fine.
Now
 a) I have deployed "General/SampleApplication.pkg" on a Windows machine. 
 b) Downloaded "General/SampleApplication.pkg" from Safari/Firefox browser, but getting back default package icon instead of my own custom icon (icon.jpg).

Any idea, how to preserve the custom icon after downloading it.

Thanks,
Satyanarayana Chebrolu
