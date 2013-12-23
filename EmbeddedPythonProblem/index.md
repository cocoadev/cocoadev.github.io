---
layout: page
title: EmbeddedPythonProblem
---

December 15, 2007

Please excuse me if you think this is a Python problem and not one for CocoaDev, but I'm trying to put an embedded Python interpreter into Objective-C and I get to watch it crash every time when importing the PyObjC libraries.  Here's the simplest possible code that causes the crash:
    
Py_Initialize();
PyRun_SimpleString("import objc");
Py_Finalize();


It causes the following error when crashing: "Assertion failed: (autoInterpreterState), function PyGILState_Ensure, file Python/pystate.c, line 563."

Does anyone see what I'm going wrong? -G

----

Take a look at Xcode's Python template/py2app's ObjC stub to see how they embed, initialise and invoke the Python interpreter. Pay particular attention to GIL (Global Interpreter Lock) related code, as that's where your current problem is.

If you're still stuck after that, ask on the PythonMac-SIG mailing list [http://mail.python.org/mailman/listinfo/pythonmac-sig].

