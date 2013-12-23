---
layout: page
title: ChangeOwnerOfFile
---

The task is to change  Posix Permissions of file owned by any user from application launched by any user... to r**r**r** - to process files in the shared folder...

using NSFileManager to change  posix permissions works fine only when file is owned by user who launches application

Is there any instruments to do such a thing... Cocoa, Carbon, CF doesn't matter...  C++\Objc-C any language... 

maybe there is a way to start chmod under super_user... let's do some hack =)


----

Think about it.  You are trying to change the permissions on a file the user doesn't own.  This is not allowed for very good reasons.  NEVER, EVER DO THIS TO FILES THAT ARE NOT YOURS.

If it IS your file that you're after, again, think.  You want to perform a privileged operation, and you want to change POSIX permissions.  SecurityFramework and chmod(3) are your tools.  Go read the documentation to figure out how to use them.

