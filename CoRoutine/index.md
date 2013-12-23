---
layout: page
title: CoRoutine
---

Coroutines are a generalization of subroutines that allow multiple entry points and suspending and resuming of execution at certain locations. A coroutine can pass control to another coroutine, and when control is passed back to it, it will resume where it left off earlier. Coroutines are especially useful for performing certain kinds of tasks, such as cooperative tasks, iterators, infinite lists, and pipes.

See Wikipedia for a more comprehensive explanation: http://en.wikipedia.org/wiki/Coroutine

CSCoroutine is a simple implementation of coroutines for Cocoa.

DPCoroutine [http://www.dpompa.com/?p=83] another Cocoa implementation that uses libcoroutine [http://www.dekorte.com/projects/opensource/libCoroutine/].
May not work properly under Leopard.

