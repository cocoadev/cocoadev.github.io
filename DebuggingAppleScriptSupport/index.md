---
layout: page
title: DebuggingAppleScriptSupport
---



You can turn on CocoaScripting debugging with the     NSScriptingDebugLogLevel preference. This preference can be set for a single app or for the entire system:
    
defaults write NSGlobalDomain NSScriptingDebugLogLevel 1
defaults write com.myApp NSScriptingDebugLogLevel 1

defaults delete NSGlobalDomain NSScriptingDebugLogLevel
defaults delete com.myApp NSScriptingDebugLogLevel


You can turn on the AppleScript engine's event tracking by setting environment variables. You can do this on a per-executable basis by double-clicking the icon for the executable.
    
set AEDebug=1
set AEDebugSends=1
set AEDebugReceives=1

unset AEDebug
unset AEDebugSends
unset AEDebugReceives


You can examine **NSScriptSuiteRegistry**, **NSScriptClassDescription**, and **NSScriptCommandDescription** to make sure your classes and commands were read properly from the the *.script**' files.

