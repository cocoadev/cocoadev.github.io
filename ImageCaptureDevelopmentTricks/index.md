---
layout: page
title: ImageCaptureDevelopmentTricks
---

Developing a driver for ImageCapture is tricky, and there is little info on it. Including few work arounds for common  problems.  This page contains some information on making an ImageCapture scanner plugin, and some trick that might help

1) If you have trouble with breakpoints, Don't start the debugger with breakpoints enabled.use the 'Deactivate' button in debug view to disable breakpoints, and enable them once the driver is running

2) If you are developing a driver, and an existing (matching) driver exists on the system, ImageCapture will load it.  Simply moving the driver to the trash doesn't fix this problem. The driver is still loaded, but from the trash. This is because the driver loaded is not  a file reference, it's a 'file alias' (FSRef). you must trash the driver, then empty the trash, for you debug/test driver to load properly.

