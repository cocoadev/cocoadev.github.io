---
layout: page
title: DirectoryInResourcesDirectory
---

Hey all.

I am making a project that requires some plugins to be installed from the Resource directory of my app to the Application Support dir. This is easy enough when these are just files. But how do I get my bundle plugins that are really directories to copy to my Resources directory in my app during a copy phase? What ends up hapening is the files inside the directory end up getting copied...but that is pointless. Adium does it. They include folders in the copy phase. They are blue and not Tan...how do I get those blue folders in my copy phase? Thanks.

--ZacWhite

When you drag the plugin to your Xcode project, there's a sheet that pops down with options. At the top is a pair of radio buttons. By default, the one with a title like, "Make groups for added folders". Click the one that says, "Make folder references for added folders", and then Xcode will copy the folder instead of its contents.

Sweet, worked like a charm. So simple! Thanks a lot.

--ZacWhite

