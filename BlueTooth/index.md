---
layout: page
title: BlueTooth
---



http://www.diggory.net/grazing/bluetooth_logo.gif Bluetooth 

is a short range wireless communications system.

----

* bluetooth site - http://www.bluetooth.com/
* apple bluetooth mailing list - http://lists.apple.com/mailman/listinfo/bluetooth-dev
* apple bluetooth developer page - http://developer.apple.com/hardware/bluetooth/
* preliminary "foundation" docs - file:///System/Library/Frameworks/IOBluetooth.framework/Versions/A/Resources/English.lproj/Documentation/Reference/masterTOC.html
* preliminary UI docs - file:///System/Library/Frameworks/IOBluetoothUI.framework/Versions/A/Resources/English.lproj/Documentation/Reference/masterTOC.html
* FindingBluetoothAvailability

----

It has less throughput and a shorter range than AirPort - but uses less power and is more flexible.  For example - there does not need to be a base station - devices create ad hoc piconets (of up to 7 devices) - and piconets  can merge into larger scatternets.

For these reasons it is becoming commonplace in digital devices (especially portable ones.)

Whereas AirPort provides one service - a TCP/IP network - bluetooth devices can offer a variety of services.

Bluetooth devices conform to various pre-defined profiles.

Bluetooth has its own service discovery system that allows bluetooth devices to discover and connect to other BlueTooth devices' services. ( for a similar concept see RendezVous service discovery)

Apple's implementation of Bluetooth now supports SCO links (as of Bluetooth v1.5 (5th Feb 2004)!  Yay - Headset Support.

----
some interesting ObjC classes:

* IOBluetoothDevice - a remote device
* IOBluetoothOBEXSession - an ObEX Session with a device
* IOBluetoothDeviceSelectorController - UI for selecting a device
* IOBluetoothPairingController - UI for pairing with a device
* IOBluetoothServiceBrowserController - UI for finding a service on a device
  



--DiggoryLaycock

Seems to work OK here. RFCOMM seems to default to a MTU of 246 for incoming stuff - is it possible to change this anyone? Tim Ellis, Ultralab

Note that these classes are not thread safe, when trying to get bluetooth scanning working in a separate thread I discovered that messages from the main thread of the application are simply not answered. The system log reveals that an NSDistantObject is objecting to being called from the wrong thread. --Alf Watt IStumbler

----

I've just spent some time trying to get to grips with OBEX Push using the Obj C Bluetooth classes, and have posted an example project at http://chrisblunt.com/documentation.php?category=2 It's a quick app knocked up to choose a Bluetooth device and push a file to it. Hopefully it'll give people something to work from; documentation seems to be really vague and hard to come by for this area!

Direct file download: http://chrisblunt.com/assets/documenta/resources/Bluetooth01.zip

--Chris

----

Object Exchange (OBEX)

http://www.irda.org/standards/specifications.asp
v1.3 spec - http://www.irda.org/standards/pubs/OBEX13.zip

Originally developed by the InfraRed Association.  

A Protocol for exchanging objects between electronic devices.  'Objects' here does not refer to cocoa-style oo software objects, but data and meta-data (OBEX headers) wrapped into a single package for intra-machine exchange. (e.g. 'Beaming' a vCard to a PDA)

Used most commonly as a quick file transfer method between BlueTooth, Infrared and other devices.

Apple has provided ObjectiveC objects for OBEX over Bluetooth

file:///System/Library/Frameworks/IOBluetooth.framework/Versions/A/Resources/English.lproj/Documentation/Reference/IOBluetoothOBEXSession/Classes/IOBluetoothOBEXSession/index.html

----

An OBEX session is a representation of a  session between your mac (the client) and some remote BlueTooth device that supports OBEX (the server).

For more information on the OBEX protocol, and how to behave in an OBEX session.

you can **connect**  to a server, **disconnect** , **put** to the server, **get** from the server  , **set path** on the server  and **abort** an active command using the methods in this class (once you have passed it an IOBlueToothDevice).

----

I've written an ObjectiveC framework, "LightAquaBlue", that has classes for running OBEX client and server sessions in a way that's easier to handle than with OBEXSession. OBEXSession is quite low-level, so it's powerful but requires a lot of knowledge about OBEX and how it works in detail -- which I guess is why Apple added the higher-level OBEXFileTransferServices class. OBEXFileTransferServices is pretty handy but it can only handle the common OBEX use cases -- it can only connect to OBEX Object Push and File Transfer services, and it doesn't let you specify custom OBEX headers (e.g. if you want to specify your own 'Description' header). So, if you need to connect to other types of OBEX services, and send your own custom OBEX headers, this can be done with the BBOBEXClientSession in LightAquaBlue. Similarly the BBOBEXServerSession class allows you to run an OBEX server and respond with custom OBEX headers.

The LightAquaBlue page at http://lightblue.sourceforge.net/LightAquaBlue/index.html has the documentation and download links. The package includes example code for running OBEX clients and servers.

