---
layout: page
title: KeyBindings
---



Cocoa's default key bindings are here: <code> file:///System/Library/Frameworks/AppKit.framework/Resources/StandardKeyBinding.dict </code>

The file <code>~/Library/KeyBindings/DefaultKeyBinding.dict</code> allows you to override the defaults.

Here's Apple's documentation on this topic:
http://developer.apple.com/documentation/Cocoa/Conceptual/BasicEventHandling/Tasks/TextDefaultsAndBindings.html

Here's everything I've figured out about the file format so far:
    
Syntax notes:
     Modifiers:
         ^ : Ctrl
         $ : Shift
         ~ : Option (Alt)
         @ : Command (Apple)
         # : Numeric Keypad
        
     Keycode formats:
         \NNN   = Octal
         \UXXXX = Hex
            
     Printing characters (including space) can be specified by 
         using the character itself.
            
     Keycodes for non-printing keys:
         These are equal to the ASCII codes:

         Backspace:  \U0008
         Tab:        \U0009
         Escape:     \U001B
         Enter:      \U000A (newline) or \U000D (cr)?

         Other non-printing keys are represented by "unicodes" 0xF700-0xF8FF.  The
         complete list can be found in the NSEvent.h header file.  The availability of 
         various keys will be system-dependent.

         Up Arrow:   \UF700
         Down Arrow: \UF701
         Left Arrow: \UF702
         Right Arrow:\UF703

         F1:         \UF704
         F2:         \UF705
         F3:         \UF706
         ...
         F12:        \UF70F
         ...
         F35:        \UF726

         Insert:     \UF727
         Delete:     \UF728  (also ASCII code \177)
         Home:       \UF729
         End:        \UF72B
         Page Up:    \UF72C
         Page Down:  \UF72D

         Print Screen: \UF72E
         Scroll Lock:  \UF72F
         Pause:        \UF730
         SysReq:       \UF731
         Break:        \UF732

         Menu:         \UF735
         Help:         \UF746

     Sub-structure syntax:
         Nested dictionary (for multi-key bindings like ^X^S):  
             { key1 = val1; key2 = val2; ... }
         Nested array (for binding to a sequence of commands (??)):
             ( elt1, elt2, ... )

     The commands (like "pageUp:" and "moveToEndOfLine:") are Action methods
     of the NSResponder class.


----

You can enter the code for any key by pressing Ctrl-q first. For example to set a keybinding for F1 you would hit Ctrl-q, then type F1 to insert the squiggley symbol that corresponds to the Unicode value for F1  into your keybindings file.


You can bind any standard NSResponder message and expect it to work.     selectLine: and     selectWord: are quite useful...

