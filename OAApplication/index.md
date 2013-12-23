---
layout: page
title: OAApplication
---

Part of the OmniAppKit.

OAApplication is a subclass of NSApplication that provides some useful functionality for applications using the OmniAppKit.

Mainly it is useful for the fact that it triggers <code> [OBPostLoader processClasses]</code>, which you need to use OmniAppKitPreferences.

Also, though, it defines some methods to access the preferences dialog, the software update checking framework that OmniAppKit provides, and the OAInspector.

Use it by setting your main application class to OAApplication instead of NSApplication in your target settings window.

