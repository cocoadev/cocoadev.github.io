---
layout: page
title: MultiplyKeyConfiguration
---

This code is a keydown code for 1 key
    
case 32:
            [self toggleAnimation];
            break;

Can somebody help me with how to toggle with multiple key configurations, like SHIFT and SPACE together?

----

Check this page out: TestForKeyDownOnLaunch
You need to link against Carbon to get it to work, but it *does* work.

