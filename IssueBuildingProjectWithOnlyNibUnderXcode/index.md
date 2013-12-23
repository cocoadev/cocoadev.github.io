---
layout: page
title: IssueBuildingProjectWithOnlyNibUnderXcode
---

Hello all,

I am trying to build a project which only has a Nib at the moment. I am using Xcode 2.1 and building a Universal Binary. A scenario that is not covered in Apple's documentation has popped up, and I am at a loss.

Here is the error I get in my Xcode build log.

    
CpResource "/tmp/IT Cabinet.dst/Applications/IT Cabinet.app/Contents/Resources/IT Cabinet.app" "build/IT Cabinet.app"
    mkdir "/tmp/IT Cabinet.dst/Applications/IT Cabinet.app/Contents/Resources"
    cd "/Users/cgiddings/Documents/Development/Cocoa/Current Projects/IT Cabinet"
    /System/Library/PrivateFrameworks/DevToolsCore.framework/Resources/pbxcp -exclude .DS_Store -exclude CVS -exclude .svn -preserve-hfs-data -resolve-src-symlinks "/Users/cgiddings/Documents/Development/Cocoa/Current Projects/IT Cabinet/build/IT Cabinet.app" "/tmp/IT Cabinet.dst/Applications/IT Cabinet.app/Contents/Resources"
pbxcp: error: destination '/private/tmp/IT Cabinet.dst/Applications/IT Cabinet.app/Contents/Resources/IT Cabinet.app' is inside source '/private/tmp/IT Cabinet.dst/Applications/IT Cabinet.app' (not copied)


Can anyone offer any suggestions? I very much appreciate it.


ChrisGiddings

----
*It appears that you are trying to copy IT Cabinet.app inside itself. Make sure that the "Target Membership" checkbox is unchecked in your Xcode project. (You can't just look at the Executables group, you have to look inside your project group at the Products "folder") --JediKnil*

----

I am such a doofus. Thank you very much.

ChrisGiddings

