---
layout: page
title: AutoCompleteInProjectBuilder
---



Did you know you can use the "F5" key to "auto-complete" strings in ProjectBuilder.app and in the Open panel? When editing a source file in ProjectBuilder, you can use F5 to complete to other words in the source file. This seems to only work in ProjectBuilder. When you type in the "Go to:" field in Open or Save panel, you can use F5 to match to files (i.e., typing "/Dev" and F5 matches to /Developer). This seems to only work in Cocoa apps. 

-- MikeTrent 

----

Have you ever wanted to use "esc" to perform auto-completion in ProjectBuilder (or missed using "esc" for auto-completion like in the NeXTSTEP days)? You can add that feature to ProjectBuilder with this little trick:

(This is from a mailing list post by Chuck Pisula.)

Here is a trick for doing a form of completion I find very useful.

In "~/Library/KeyBindings/DefaultKeyBinding.dict", place:

    
	{
	    "\033" = "complete:";
	}


After doing this, you will be able to <esc> complete strings in the 
current file you are editing.  The completion will not do any smart like 
complete to the possible methods of an instance.  It simply seems to 
complete to any strings it has already seen in the current file.  I love 
it though!

For more info you can also look at the Text System Defaults and KeyBindings documentation in the Cocoa section of the on-line developer 
docs where all this is  documented.

-- JensBaumeister & MikeTrent

This is now accesible directly from Project Builder (as of Dec 2002 tools) - go to the key bindings section of the preferences page, choose the 'text key bindings' tab and the 'complete' action is under 'text editing' -- TomSci

----

I've written an open source Cocoa/Objective-C class that handles absolute path auto-completion in any NSTextField:  DTCPathView

// DanielToddCurrie

----

If you are still using ProjectBuilder, I wrote a little plugin that uses the indexing system to do some slightly better autocompletion based on symbols in your project. This is no where near the level of CodeSense in Xcode, but I found it handy. http://culater.net/software/PBXtra/PBXtra.php --MikeSolomon

