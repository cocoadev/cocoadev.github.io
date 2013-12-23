---
layout: page
title: DataCrux
---

An Objective-C framework to easily store and retrieve data objects in an embedded SQL database without a server process or query strings.
http://treehouseideas.com/datacrux/

If i understand correctly, it's like CoreData for 10.2-10.3 ? maybe less powerfull than CoreData but really handy.

----

That's a pretty good description, yes. - Scott

----

Is there a way to do what DataCrux does and have a server process, so 2 or more applications can share the same data/database?  What about over a network?  I'm looking at CoreData, DataCrux, and others (WebObjects)

----

SQLite isn't explicitly designed for that, so nothing like that is built into DataCrux. You'd have to mediate the remote connections yourself. WebObjects can easily do this, but it's all Java.

----

Actually, SQLite is designed to let multiple processes read from the same file.  Writing can cause confusion, but it shouldn't cause a corruption.

