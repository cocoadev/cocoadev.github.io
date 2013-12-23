---
layout: page
title: GDBAndMacllocDebug
---

Does anybody know how to run MallocDebug on an app and at the same time be running gdb from IDE on the same app?

Try using "set environment" in the console to set the MallocDebug env vars.  Then do kill and run and see if that'll start your app with the env vars set.  Otherwise set the env vars before starting PB, or run gdb from the command line.

