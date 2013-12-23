---
layout: page
title: IOBlueToothDevice
---

IOBlueToothDevice
file:///System/Library/Frameworks/IOBluetooth.framework/Versions/A/Resources/English.lproj/Documentation/Reference/IOBluetoothDevice/Classes/IOBluetoothDevice/index.html
----

An object representing one remote Bluetooth device.

This class allows things such as finding out a device's name, baseband address, etc..

Note that this class is not thread safe, when trying to get bluetooth scanning working in a separate thread I discovered that messages from the main thread of the application are simply not answered. The system log reveals that an NSDistantObject is objecting to being called from the wrong thread. --Alf Watt IStumbler

