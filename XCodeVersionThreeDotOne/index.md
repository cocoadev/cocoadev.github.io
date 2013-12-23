---
layout: page
title: XCodeVersionThreeDotOne
---



Gotchas...

*If you are using a home brewed pbplugin/xcplugin you have to recompile it to be garbage collection compliant or else General/XCode 3.0 will not load it (Console will log "WARNING: skipping plugin for GC"). You also have to add the key/string pair General/XCGCReady/YES to the Info.plist of the plugin bundle. --zootbobbalu
