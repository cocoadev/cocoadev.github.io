---
layout: page
title: ProjectBuilderShell
---



ProjectBuilder has a feature that allows you to execute shell commands from within the file you are editing.

An example of the type of things you can do with this feature is to insert the contents of a file into the file you are currently working on. (i.e. cat /tmp/somefile). You execute the command by starting with a blank line somewhere inside you source. You then type a command (with arguments) and then hit CONTROL+"r". Standard output is inserted below the command you just typed out (I wish the shell would let you set an option where executed commands are erased).

If you echo $SHELL you find out that the shell is "tcsh". 
If you pwd ~ you find out that the home directory is /private/tmp

I wouldn't mind starting a discussion about more of the things you can do with this shell.

I have been looking all over the net to find information about the shell environment, but I haven't found anything yet. I would be nice if Apple implemented an environment variable that stores the full path of the current file being edited. 

----
One usesful(although not difficult nor original) thing you could do perhaps is to answer "uh, how big is 2^32 again?" and so on... try "echo "2^32" | bc" -- englabenny@macnytt.com

----
The command you're executing is acutally written to a file in /private/tmp and then executed.  Try doing a "sleep 50" and then opening any new files that appear there.  Maybe one way of getting the currently edit file into the shell environment would be to try this "setenv CUR_FILE /User/myFile; runScript;".  But this may not work, when I try "setenv foo bar; echo foo;" I get no output.
--SaileshAgrawal

----
The Project Builder release notes (by now, the "Older Release Notes") have a LOT of detail on this stuff.  

There are ways you can get the path to the file in the editor.  (The string %%%{PBXFilePath}%%% will be replaced with the path to the current file.  Many other %%%{...}%%% substitutions are also supported.)

There are ways to get PB to add new menus with items that invoke user-defined scripts.  (In /Developer/ProjectBuilder Extras/ExampleScripts you can find a bunch of example scripts as well as an example startup script that can build the menus.)

What shell gets run is under your control for the menu item scripts.  Just use the standard #! syntax in your script file.  (And for MPW-inspired Ctrl-r buffer content execution stuff if you want to actually type a #! line and make sure it is the first line selected when you hit Ctrl-r it will use that shell too, at least in the current release).  Use python or perl or whatever.  Hell, use osascript and write AppleScripts.

And, finally, a shameless plug:  Do you like this feature of PB?  Would you like it in any Cocoa application?  The feature was first implemented in TextExtras and is available there in a way that makes it work in ANY Cocoa application that uses the standard text system (like TextEdit, Mail, iChat, whatever).  

http://www.lorax.com/FreeStuff/TextExtras.html

- Mike Ferris

----

Hey Thanks Mike!

%%%{PBXFilePath}%%% is AWESOME!!!!!!!!!!

After playing with this for a bit I came to realize an easy way to avoid having to deal with the mess that gets left behind when you execute shell commands from within source files. All you have to do is cord off a section at the top of the file and command away!!!!

    


/* command section starts

~/Library/PBCommands/edit_this_file -p %%%{PBXFilePath}%%%
edit_this_file output: /Users/bob/Dev/MyProject/MyObject.h being processed

command section ends */ 






----

About the only thing I'd want to change about this would be for it to leave the originally selected text selected after the shell command completes.  Then I could just hit the Delete key to get rid of the original, and I'd have one less reason to use vi.

For example, to comment a block of text, I select the following lines and hit Control-R:

    
sed 's@^@// @' << JABBERWOCKY
      if ( [task respondsToSelector:@selector(shouldCancel)] )
      {
         continueProcessing_ = ![task shouldCancel];
      }


I get the commmented-out lines appended to the bottom:

    
sed 's@^@// @' << JABBERWOCKY
      if ( [task respondsToSelector:@selector(shouldCancel)] )
      {
         continueProcessing_ = ![task shouldCancel];
      }
   }
//       if ( [task respondsToSelector:@selector(shouldCancel)] )
//       {
//          continueProcessing_ = ![task shouldCancel];
//       }
//    }
// 


If the originally selected text remained selected afterwards, it would be very easy form me to kill it once I determined that the results were what I expected.  As it is now, I have to manually select the original lines and delete them.

