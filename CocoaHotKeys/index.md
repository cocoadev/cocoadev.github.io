---
layout: page
title: CocoaHotKeys
---

I've successfully gotten hotkeys working in cocoa, as long as a modifier is supplied with it. Now I've been trying to get hotkeys to work without modifiers. I'm trying to make F-Keys work without needing to use a modifier with them. Any ideas on how that's done?

-- KentSutherland

----

You need to check for NSFunctionKeyMask and pass kEventKeyModifierFnMask to RegisterEventHotKey().

-- Wevah

