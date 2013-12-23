---
layout: page
title: IOBluetooth
---



IOBluetooth is the API for Apple's BlueTooth implementation.

IOBluetoothUI is the API for Apple's Built-In Bluetooth Paring, Device Selection, Object Push and Service Browsing windows.

IOBluetooth is not thread safe, you must issues all requests to the API from the same thread. If you are using a secondary thread to query bluetooth devices then the IOBluetoothUI Windows will not function. 

In 10.4 Apple introduced OBEXFileTransferServices, which facilitates file transfer to bluetooth devices, unfortunatly the documentation isn't very clear and it can be difficult to get file transfers working perdictably. Does anyone know of example sources for this class?

