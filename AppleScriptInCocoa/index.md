---
layout: page
title: AppleScriptInCocoa
---

Anybody have any suggestions on how I can easily use AppleScript to execute one line of code?

I'm trying to get my program to tell System Preferences to quit, and I think the best way to go about doing so is by using AppleScript.

Any hints or suggestions?

-- KentSutherland

----

Try this:

NSString *scriptText;

NSAppleScript *quit;

scriptText=@"tell application \"System Preferences\" to quit";

quit=[[NSAppleScript alloc] initWithSource:scriptText];

[quit executeAndReturnError:nil];

[scriptText release];

[quit release];

--- greez shadow71

----

That will only work on 10.2 and higher, though. On 10.1 and earlier the class was called _NSAppleScript and was private (for god knows what reason why). [ API is often kept private until its implementation or public interface is finalized. Why? In a dynamic-library environment you can't change API once it ships and ask clients to re-write their apps to conform to the new API (which is what the unix/Linux community traditionally does); users commonly run older programs against newer dylibs. So once a client dynamically links in an API, it must remain for the forseeable future. As a reuslt soon-to-be-public API is often tested privately before being publicized. No doubt _NSAppleScript is one such example. -- MikeTrent ] 

Anyway, for quitting System Preferences you could (should?) use the Process Manager.

-- FinlayDobbie

