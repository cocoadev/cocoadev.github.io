---
layout: page
title: UIHardware
---



Part of the iPhone General/UIKit framework

<code>+ (General/CGRect)fullScreenApplicationContentRect;</code>

Returns the size of the phone's screen.

<code>+ (int)deviceOrientation:(BOOL)flag;</code>

Returns the orientation of the phone, natch. (With the flag set, it does something on the corners.) The General/UIApplication method deviceOrientationChanged is called when the phone is rotated.

This seems to work only if the application is started from the General/SpringBoard GUI (rather than from an ssh command line).


* 0 - Phone is flat (on a table?) with screen upwards
* 1 - Phone is in normal position
* 2 - Phone is rotated upside-down
* 3 - Phone is rotated to the left
* 4 - Phone is rotated to the right
* 5 - Phone is changing orientation ? 
* 6 - Phone is flat, with screen downwards - very useful!


----

Was this removed/deprecated/replaced ? it does not exists in the 2.0 sdk anymore.

----
Not removed, merely moved, to General/UIDevice.


----
The file itself WAS removed General/UIHardware.h from General/UIKIt , and General/UIDevice does not return any information about screen size  , only returns device orientation and device info.

----
I missed the screen size method. But doesn't General/UIScreen provide that information?

----
1. screen size 2.0: General/[[UIScreen mainScreen] bound]
2. General/UIHardware is still in 2.0 but it is hidden by official header file. So that for General/AppStore oriented developer, it is same as removed.
