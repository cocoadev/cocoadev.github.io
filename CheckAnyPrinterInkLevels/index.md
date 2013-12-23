---
layout: page
title: CheckAnyPrinterInkLevels
---

      I was curious if there was any possible way to use cocoa to check any printer's ink levels. If there is, could someone lend me the code and maybe some directions on how to put it in an application? I'm a little new, so please help me out! Thanks!

----
See "CUPS".

----

Hmmm...helpful! I found this, which is for Epsons:

http://lists.freestandards.org/pipermail/printing-driver/2005-May/001014.html

The "escputil" is already built into Macintoshes so would help you definately. Maybe there are similar tools for Canon, HP, etc??? Here's a quote from that link:

7) Various improvements have been made to escputil, the command-line
     Epson inkjet printer management utility.  The changes are quite
     extensive, and we would like feedback on their operation.

     * A new extended ink information command has been added.  This
       command, which is supported on printers with separate
       individual ink cartridges, prints additional information about
       the ink cartridge in addition to the amount of ink present.  On
       older printers, it simply returns the standard ink
       information.

     * Ink level detection now works on all known supported printers.
       Previously it did not work on some newer printers.  However, it
       is somewhat slower on these printers than it is on older
       printers that support the faster ink detection method.

     * Ink level detection now prints the correct ink color names for
       the printer model.

     * It may be necessary to use the -m option (to specify the
       printer) with certain printers, even with commands such as -i.
       This was previously not the case.  In general, it should not be
       necessary.

     * It is now no longer necessary to use the -u flag with certain
       printers in most cases.  The utility now autodetects the
       printer model and generates appropriate commands in all cases.

       If you supply a printer queue (with the -P option) rather than
       a raw device (with the -r option), you will still need to
       provide the -u flag, as the utility cannot detect the actual
       printer connected to a queue.  However, bidirectional commands
       (ink level, identify, printer status, and alignment) do not
       require the -u flag any more.

     * escputil now successfully detects ink level, printer model, and
       status even if the printer is out of ink.

