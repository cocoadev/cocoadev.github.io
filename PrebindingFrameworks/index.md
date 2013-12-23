---
layout: page
title: PrebindingFrameworks
---



From the dyld Release Notes (Tiger):

** Prebinding is deprecated **

In Mac OS X 10.4, unprebound applications launch about as fast as prebound applications. Therefore, application developers need no longer spend any time trying to built prebound applications. -- DevinBayer

----

PrebindingFrameworks is an optimization that is worth investigating. Read this document first before doing anything else:

file:///Developer/Documentation/ReleaseNotes/Prebinding.html

PrebindingFrameworks can be tricky because the address ranges of frameworks under development are constantly moving targets (no pun intended). You must have a game plan before assigning base virtual memory addresses to the frameworks used in a given application. The section in the document above that describes the importance of avoiding address overlap should be looked at closely. In order to get a handle on the address range each framework requires (for a given application), you have to keep tabs on the size of the address space that each MachO file takes up.     otool can peer into the inner workings of a framework and help you space each framework�s address range appropriately. MachOInfo is a tool that generates a report for a collection of frameworks. 

MachOInfo outputs the base virtual memory address, the length of the address range and the headroom for each framework in the collection. The base virtual memory address is defined by setting the linker flag     seg1addr for a given framework target.     -seg1addr 0x10000000 assigns a base address of 0x10000000. 

The address range starts at this base address. The length of the address range can change while the framework is being developed. In order to get the most out of MachOInfo, create a new directory in you project folder and place links to the frameworks you would like to prebind in a target. For example, if you have a target with two frameworks that you would like to prebind do the following: (WebApp is a project folder for an application named WebApp)


*build MachOInfo into a command line tool (target of type 
�tool�) named �machoinfo�
*Create a new directory in your project folder named something like �WebAppFrameworkLinks�
*    cd to the new directory
*Place links to the two frameworks in the new directory
*run     machoinfo for the current directory


    
mkdir ~/Dev/WebApp/WebAppFrameworkLinks
cd ~/Dev/WebApp/WebAppFrameworkLinks
ln �s ~/bin/CustomWebKit.framework CustomWebKit.framework 
ln �s ~/bin/CustomAppKit.framework CustomAppKit.framework
machoinfo .

outputs: 
    
address     length      headroom    name
0x10000000  0x000baef8  0x0ff45108  CustomWebKit              
0x20000000  0x00003d40  unknown     CustomAppKit


Just for fun, generate a report for /System/Library/Frameworks. Here�s the output for OS X v10.2.6.

    
address     length      headroom    name
0x7e360000  0x000baef8  0x08e35108  WebKit              
0x87250000  0x00003d40  0x0092c2c0  DVComponentGlue     
0x87b80000  0x0000a0dc  0x04a35f24  DrawSprocket        
0x8c5c0000  0x0000f32c  0x00050cd4  FWAUserLib          
0x8c620000  0x0001fc84  0x0013037c  AGL                 
0x8c770000  0x0000228c  0x0002dd74  AppKitScripting     
0x8c7a0000  0x0000675c  0x008698a4  ExceptionHandling   
0x8d010000  0x00002280  0x0004dd80  Scripting           
0x8d060000  0x0001cfe4  0x02f8301c  DiscRecordingUI     
0x90000000  0x0013427c  0x00000000  System              
0x90130000  0x000ee7e8  0x00271818  CoreFoundation      
0x90490000  0x0007a2c4  0x000b5d3c  IOKit               
0x905c0000  0x00002278  0x0000dd88  CoreServices        
0x905d0000  0x00070f04  0x0002f0fc  IOBluetooth         
0x90670000  0x0000228c  0x002fdd74  ApplicationServices 
0x90970000  0x00010058  0x000cffa8  AppleTalk           
0x90a50000  0x00025a20  0x0000a5e0  SystemConfiguration 
0x90a80000  0x00002264  0x0000dd9c  Carbon              
0x90a90000  0x0002ad50  0x000052b0  OpenGL              
0x90ac0000  0x00049020  0x00006fe0  AppleShareClientCore
0x90b10000  0x000338c0  0x0001c740  DirectoryService    
0x90b60000  0x00009910  0x001366f0  JavaVM              
0x90ca0000  0x0002a910  0x000056f0  AppleShareClient    
0x90cd0000  0x00002260  0x0000dda0  Cocoa               
0x90ce0000  0x000501f0  0x0000fe10  CoreMIDIServer      
0x90d40000  0x000e8000  0x00068000  InterfaceBuilder    
0x90e90000  0x0000bca4  0x0101435c  ScreenSaver         
0x91eb0000  0x00002910  0x000bd6f0  AudioUnit           
0x91f70000  0x001bc0e4  0x00a73f1c  QuickTime           
0x92ba0000  0x003dd1a4  0x000f2e5c  Security            
0x93070000  0x006d1b28  0x00000000  AppKit              
0x936e0000  0x0000f25c  0x00360da4  PCSC                
0x93a50000  0x000fda7c  0x00322584  AddressBook         
0x93e70000  0x00049b5c  0x001464a4  LDAP                
0x94000000  0x00098ca4  0x0008735c  DiscRecording       
0x94120000  0x00018efc  0x00097104  PreferencePanes     
0x941d0000  0x00015db0  0x0000a250  IOBluetoothUI       
0x941f0000  0x00129040  0x00026fc0  Message             
0x94340000  0x00170378  0x0000fc88  Kerberos            
0x944c0000  0x000129d8  0x007dd628  TWAIN               
0x94cb0000  0x000c24dc  0x0000db24  AudioToolbox        
0x94d80000  0x000aa038  0x00195fc8  CoreAudio           
0x94fc0000  0x0009d364  0x00302c9c  GLUT                
0x95360000  0x0007de28  0x00000000  AppleScriptKit      
0x953d0000  0x00052b30  0x009cd4d0  CoreMIDI            
0x95df0000  0x00017000  0x00b89000  JavaEmbedding       
0x96990000  0x00004b08  0x0047b4f8  ForceFeedback       
0x96e10000  0x009b2d44  0x0062d2bc  vecLib              
0x97df0000  0x00213140  unknown     Foundation


A couple of frameworks overlap (zero headroom), but the frameworks that overlap do not appear to be frameworks that work together.  --zootbobbalu

