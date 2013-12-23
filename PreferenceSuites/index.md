---
layout: page
title: PreferenceSuites
---

using NSUserDefaults' method addSuiteNamed: method, you can add a preference suite that does not belong to your application.

This way, you can share preferences between two apps like a config app and a daemon, all through the nice NSUserDefaults interface. (But, if you want to set individual prefs in a suite, you'll want to use CFPreferences.)

