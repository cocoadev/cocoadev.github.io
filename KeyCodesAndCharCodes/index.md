---
layout: page
title: KeyCodesAndCharCodes
---

Following a lost posting on the cocoa-dev lists, I figured I'd ask here, as I feel more comfortable railing on for a fair time and still having a chance of getting an answer! http://goo.gl/OeSCu

This is all for a little internal project, but I can so easily see other uses.  We're trying to send key presses to any application, which can be achieved by CGPostKeyboardEvent - in CGRemoteOperation.h - for the frontmost app, and AXUIElementPostKeyboardEvent for any app, although the whole AXUI thing is a totally different kettle of fish and one I'll tackle later.

However, both PostKeyboardEvent commands need a couple of variables - CGCharCode and CGKeyCode for every key you want to send.  The header kindly gives examples for left shift and v, but that isn't too much use!  Does anyone know of a list of these values?  And if not, does anyone know if the CGKeyCode is similar to any other keycode or ascii value systems out there - I think '56' was shift, and '6' was z, and '9' was v.

Or failing that, how would I go about capturing such codes for any key presses?  Otherwise I can see myself feeding values into Key Caps, trying to get keys to go down and working it out that way....

Thanks, all.  --Fred

----

Well, found uControl (http://www.gnufoo.org/ucontrol/ucontrol.html), which has an option to switch on keyboard debug.  This catches keyboard events and writes them to /var/log/system.log... including the CGKeyCode or the equivalent.  I'll compile a list and stick it on here at some point.

Unfortunately, this doesn't cover a couple of the special keys I was hoping it would - normal keyboard presses send a hid event, whereas the volume and eject keys send a 'special event' with 'flavor'...

More as I figure it out, although input by anyone more experienced would be welcome ;) --Fred

----

In the interests of a fully-fledged conversation with myself, I'll keep going.  It seems there aren't any obvious hooks into the system call itself, but it is possible to genereate the 'special' keys such as volume buttons, power button, and the eject key.  This is done by using evSpecialKeyMsg, in IOHIDSystem.h.  Unfortunately, I can't link to IOKit at all... I know it's intended for kernel development, but I'm running into lots of header problems.  IOKit.framework doesn't contain IOHIDSystem.h.  Kernel.framework/IOKit does, but causes tons of problems because it then calls on IOKit.framework, all the headers in Kernel.framework tying to link as if they're in IOKit.framework.  Fixing these header calls still results in non-compiling files.  The Darwin xnu versions of IOKit contain the header, but suffer the same problems as Kernel.framework.

Sigh.  It's there, I just can't link or include at all.  Are there known problems with IOKit, or am I doing something truly stupid?  --Fred

----

To my knowledge, you are prevented from crossing the kernel-userland boundary. Might it be possible to split your program into an app and a kernel... thing?

-- RobRix

----

There was a post about this on the studentdev list a while ago:

http://lists.apple.com/archives/student-dev/2002/Aug/thrd3.html#00227

SamTaylor

----

I am looking for the same thing, I would like to send some keyboard events to an application, so I use CGPostKeyboardEvent - in CGRemoteOperation.h, it works fine. My problem is to get the key code regarding the ascii character I want to send to the application.

I found an interesting exemple on Appe's website.

I have this piece of code (carbon) :

    

-(OSStatus)InitAscii2KeyCodeTable
{
    unsigned char *theCurrentKCHR, *ithKeyTable;
    short count, i, j, resID;
    Handle theKCHRRsrc;
        ResType rType;
        /* set up our table to all minus ones */
    for (i=0;i<256; i++) keytable.transtable[i] = -1;
        /* find the current kchr resource ID */
    keytable.kchrID = (short) GetScriptVariable(smCurrentScript,
smScriptKeys);
        /* get the current KCHR resource */
    theKCHRRsrc = GetResource('KCHR', keytable.kchrID);
    if (theKCHRRsrc == NULL) return resNotFound;
        GetResInfo(theKCHRRsrc,&resID,&rType,keytable.KCHRname);
        /* dereference the resource */
    theCurrentKCHR = (unsigned char *)  (*theKCHRRsrc);
        /* get the count from the resource */
    count = * (short *) (theCurrentKCHR + kTableCountOffset);
        /* build inverse table by merging all key tables */
    for (i=0; i<count; i++) {
        ithKeyTable = theCurrentKCHR + kFirstTableOffset + (i * kTableSize);
        for (j=0; j<kTableSize; j++) {
            if ( keytable.transtable[ ithKeyTable[j] ] == -1)
                keytable.transtable[ ithKeyTable[j] ] = j;
        }
    }
        /* all done */
        
        return noErr;
}

-(short)AsciiToKeyCode:(short)asciiCode
{        
    if (asciiCode >= 0 && asciiCode <= 255) return
keytable.transtable[asciiCode];
    else return -1;
}



To convert an ascii char to keycode I use the AsciiToKeyCode function.

The problem I have is for the string http://www.apple.com (for example)

I get http://wwww;apple;com for french keyboard and http;//wwww.apple.com
for american keyboard.

I know that I need to press shift on my french keyboard, but I was thinking that I don't have to know the keyboard type (french, english,...) to send a keycode, what is done by AsciiToKeyCode function.

What is wrong ???

Thanks a lot for your help.

Thierry
thierry_at_sockho.com

----

The keyCode represents the physical key on your computer keyboard.  The character code represents the character.  All your doing by giving it a keyCode is telling it to "act like your pushing the key that's 2 spaces to the left of the right shift key", which in French returns :

Try either mapping the unichar codes instead of the key codes, or use insertText

(Use your Edit >> Special Characters >> Unicode Tables tool in your menu to get the mappings. ASCII is at the begining.)

    

unsigned int unistrlen = 5;

unichar ch[] = { 0x0068, 0x0065 ,0x006c, 0x006c, 0x006f };
NSString *test = [NSString stringWithCharacters:ch length:unistrlen]; 



Chris

chrisw_at_independentnc {[(dot)]} com 

----

I wrote some code which is now in HotKeysLib, which is available at http://rogueamoeba.com/sources/HotKeysLib3.tgz (and copes with both uchr and KCHR). -- FinlayDobbie

----

This may be of some use. CGKeyCode list for all letters, numbers and some special characters - http://www.jacobward.co.uk/cgkeycode-list-objective-c/

