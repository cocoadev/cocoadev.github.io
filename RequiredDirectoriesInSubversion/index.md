---
layout: page
title: RequiredDirectoriesInSubversion
---



Ok, so I've written a small application and have decided it's high time to move it to subversion to manage tags for each version I release. Plus the redundancy of having a repository for backup and just to rollback when I do stupid things.

So the question is, what directories are needed in Subversion? 

I know I need the following:

Source Code (*.h, *.m)
XCode Project (*.xcodeproj)
Property Lists (*.plist)
Language Projects (*.lproj)

Do I need the build directory? If so, which files inside it are important?

The reason I ask is because each compile will end up changing the files in the build directory and that means there will always be updates there... I don't really wanna track those changes, just my source code. So is it safe to nix the build directory and will Xcode rebuild it (I'll just put in an svn ignore property on that whole directory). Do I need the Prefix.pch file as well?

Appreciate the help guys. Thanks.

----
Nothing in the build directory is required. If in doubt, check your stuff in, then check it out in some other location and try to build it there. If it succeeds, you're good to go.

----
Thanks a lot. I figured asking was the best route to take, that way it was documented in case someone else is looking for it in the future. Plus, I'm rather lazy tonight ;)

Thanks again. 

----
Actually, I'm sorta having some odd issues with the xcodeproj file as well. It seems to change everytime I open it up. Any good suggestions on how to sorta "fix" this?

----
Don't include the .pbxuser files. They store stuff like window positions which change constantly and don't really need to be saved.

----
Be cautious with not including .pbxuser files.  This has just bit be today as I found out that some of the project settings that I saved that are important (SCM configuration for one) are not being saved in the pbxproj file, but in the pbxuser file.

