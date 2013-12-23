---
layout: page
title: CodeCompletion
---



Does anyone know of a good code editor for Mac OS X that includes code completion with context sensitive popup menus. Almost all the All-Java editors like NetBeans/Forte, IDEA, JBuilder etc. has this feature. The main problem with these is that you can't write { }, [ ] or any char that requires a combination with the alt-key to be written... The F5 key in Project Builder is a blind code completion feature.

----

http://www.obdev.at/products/completion-dictionary/index.html

-- RobRix

----

Hum. Might just be my own stupidity getting in the way, but I can't figure out where to put the keybinding dict.  the plugin seems like it will work fine, though. ^^;;

- (id) joie;

----

Don't worry about it, it's not entirely obvious.

Mine is currently at:

/Users/username/Library/KeyBindings/CompletionKeyBinding.dict

I've found this to be extremely useful. If you're interested, I could probably send you my set of macros, some of them are very useful indeed. I've also had discussions with the author about possible extensions that he's considered... not sure when they'll be released, but they're very interesting things (and would make accessors a matter of typing the macro, hitting escape/F5, and filling in three fields or so... very handy, very rapid).

-- RobRix

----

Does anybody know where to put the PBKeyBinding.dict when it should work System wide? The ReadMe states /LocalLibrary/KeyBindings, but this sure isn't an official place. /Library/KeyBindings doesn't Work.

-- DominikWagner

----

The only PB-specific keybindings that are interpreted system-wide are in 
/Developer/Applications/ProjectBuilder.app/Contents/Resources/PBXKeyBinding.dict 
(the bundle's own settings). However, PB makes a composite dictionary out of the text system's default stuff, your text system dictionary, PB's default dictionary and your user's PB dictionary. A pretty neat trick. So if your text system entries are not overriden by the PB settings they will still be there, so it's sort of system wide. :) The Cocoa docs don't mention PB taking key bindings from anywhere else.

If you really want to make your settings for PB universal for that installation, just overwrite the dictionary in the nib file. 

-- DaveFayram

