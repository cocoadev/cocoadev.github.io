---
layout: page
title: GetProcessID
---

Getting the Process ID for a running Application (run this with NSTask)

//running app we are interested in = Terminal.app

ps -auxww | grep Terminal | grep -v grep | awk '{print $2}'


----

You should really use the approach recommended in http://developer.apple.com/qa/qa2001/qa1123.html -- FinlayDobbie

