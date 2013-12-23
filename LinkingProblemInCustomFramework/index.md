---
layout: page
title: LinkingProblemInCustomFramework
---

Hi,

I am working on a custom framework. This framework is linked to a static library, which is internally linked to a dynamic library (relocatable). Linking structure is as follows: 
foo.framework linked to libembed.a.
libembed.a refer to libcore.dylib (@executable_path/core.dylib)
libcore.dylib refers to libcomponents.dylib (and other related dylibs)(@executable_path/libcomponents.dylib)

I get the following errors when I build the framework.

warning can't open dynamic library: @executable_path/libcomponents.dylib referenced from: ../../../libs/libcore.dylib(checking for undefined symbols may be affected) (No such file or directory, errno = 2)
Undefined symbols:
<List of symbols>

Internal link command failed

All the library is available in libs folder.
Please can anyone tell me why the linker is failing.

Thanks in advance,
RV

----
This is becoming *tiresome*. People, this is **not a mailing list**. See MailingListMode, HowToAskQuestions, HowToUseThisSite, etc.

----
You may want to check that the libraries exist in the places that they need to, and that the *load commands* and *install name* (    man otool) are what you expect. It is possible that the linkage: *framework* -> *static library* -> *dynamic library* won't work anyway (the second relocation cannot be performed after linking to a static library), but you may have to wait for someone who knows more about linking than I comes along to explain this. You will probably have already noticed that the XCode tool chain makes it easier to work with dynamic libraries than static ones, and you might want to investigate some way of providing your     libembed.a as a dynamic library.

See ApplicationLinkingIssues (linked from CommonProblem ) LinkingLibrariesIntoACocoaApp StaticVsDynamicLinking and StaticLibrariesInDeployment (which may have the exact answer to your problem).

Per MailingListMode , you are warmly invited to "refactor" and clean up this page, notably to remove any cruft that I have added!

