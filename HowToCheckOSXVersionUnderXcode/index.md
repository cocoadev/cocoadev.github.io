---
layout: page
title: HowToCheckOSXVersionUnderXcode
---

I need to start a Perl script at the end of my compilation, and I need to know 10.4 or 10.5 OS (because svninfo is under /usr/local/bin or /usr/bin)

How can I do this with $ENV variable ?

----
    man sw_vers

The following snippet will return '1' if the product version contains 10.5, or '0' if it does not. The result is printed on standard output.

    sw_vers -productVersion | grep -c '10\.5'

