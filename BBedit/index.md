---
layout: page
title: BBedit
---



BBEdit is one of the more popular text editors for Mac OS, developed by BareBones Software. It's been around for a while and has aquired quite a lot of nice features.

BareBones used to offer a freeware version of BBEdit called BBEdit Lite.  It offered a limited text editing environment without most of the niceties of the full version (such as syntax coloring and scripting support, etc).

After pulling BBEdit Lite, BareBones released a text editor called TextWrangler for a little less than half the price of BBEdit.  TextWrangler uses the text-editing core of BBEdit with some of its features (such as regular expression support in the Find feature, and applescript support), and appears to be oriented toward C, C++, and Objective-C developers for the Mac.  It doesn't offer a lot of the features aimed toward web developers that are present in BBEdit full version.

http://www.bbedit.com / http://www.barebones.com

----

BBEdit Lite is still available on BareBones' ftp site - [ftp://ftp.barebones.com/pub/freeware/BBEdit_Lite_612.smi.hqx]

----

TextWrangler now is available for free from BareBones.

----

If the xcode text editor drives you nuts, you can use the xcodebuild command line command.  Make a bbedit worksheet and try this:

    
cd /Users/username/Projects/YourApp/
xcodebuild
open /Users/username/Projects/YourApp/build/YourApp.app/Contents/MacOS/YourApp


Eeew ... I know some people prefer other editors, but this solution is a great, blind, flying leap backwards!

*you can just set Xcode to open your files in BBEdit, or any other editor too, if you like.*

