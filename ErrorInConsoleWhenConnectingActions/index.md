---
layout: page
title: ErrorInConsoleWhenConnectingActions
---

When I compile my app I get this error:

    

Could not connect the action editOk: to target of class MyDocument



What's wrong?

*It means you don't have a -editOk: message in your MyDocument class. If you think you do, check the spelling, make sure it actually takes an argument (-editOk is not the same as -editOk:), make sure the capitalization is the same, etc.*

