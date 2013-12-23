---
layout: page
title: SqliteIndex
---

Does anybody know whether CoreData puts an index into sqlite stores?  I'm pretty sure sqlite doesn't do any of its own indexing... and overlooking CoreData for a minute, would it make sense to manually build some index (i.e. a b-tree) and stuff it into sqlite if sqlite doesn't automatically build one, or would it be best to go with some full blown rldb that would take care of it for me?  Suggestions?

----

According to this rather incomplete page: http://web.utk.edu/~jplyon/sqlite/SQLite_optimization_FAQ.html#indexes

*SQLite automatically creates an index for every UNIQUE column, including PRIMARY KEY columns, in a CREATE TABLE statement.... You may also explicitly create an index on an existing table using the CREATE INDEX statement. This allows dropping the index later.*

As to whether CoreData uses these capabilities properly, I cannot say.

