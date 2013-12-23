---
layout: page
title: NSClassFromString
---

A Foundation function to return the class by giving it the name as string.

    id myObj = [[NSClassFromString(@"MySpecialClass") alloc] init];
is equal to:
    id myObj = [[MySpecialClass alloc] init];

----
This is very useful when trying to use the JavaBridge (deprecated or not). Because Java classes are not integrated into the program until runtime, you have to use NSClassFromString to reach them. Example:
    
Class JavaArrayList = NSClassFromString(@"java.util.ArrayList");
id myJavaArrayList = [[JavaArrayList alloc] init];
// You can also combine these two calls, as seen above.

