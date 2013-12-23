---
layout: page
title: CodeSigningMyApplication
---

Leopard's firewall introduces the necessity for code signing, but it took me quite some time to figure out how to do that.

Here's the relevant article: http://developer.apple.com/documentation/Security/Conceptual/CodeSigningGuide/Introduction/chapter_1_section_1.html

basically you create a certificate using Keychain Access and then use the command line tool codesign, as in 

    codesign -s "my newly created certificate" myPathToAppBundle

