---
layout: page
title: MakefileInsteadOfXcodebuildTool
---


So. 'Xcodebuild' is nice, but i'd like to use makefiles. Before using gcc and pbxcp, it makes directory (mean builds) like "./build/proj.build/Release/..." self, and i dont know how to repeat this action
    
=== BUILDING NATIVE TARGET proj WITH THE DEFAULT CONFIGURATION (Release) === 
...

And some more, how to say 'xcodebuild' to use another compiler? i'm using like xcodebuild CC="/usr/bin/gcc-3.3", but it using standarrt options for gcc-4.0? how to change config?
----
GCC_VERSION=3.3.  The options you pass to xcodebuild are Xcode settings, not gcc settings.  Check out http://developer.apple.com/releasenotes/DeveloperTools/Xcode/XcodeBuildSettings.html .

