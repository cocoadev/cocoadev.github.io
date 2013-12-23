---
layout: page
title: EditingPlistFile
---

I have a plist file (any suffix, any location) and want to edit its properties.

    <key>Name</key>
<string>Steve</string>

How to do that?

----

Open it in a text editor, make the changes you want to make, and put it back.

If you want to edit it in code, you can do something similar. Use NSPropertyListSerialization to get the plist in object form, make the changes you want, then use NSPropertyListSerialization to get it back into plist form.

----
In addition, NSArray and NSDictionary's file IO methods (-initWithContentsOfFile/URL:, -writeToFile/URL:atomically:, etc.) will write a valid property list if all of the content objects are property list objects (and for dictionaries, if all of the keys are strings).

