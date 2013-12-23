---
layout: page
title: ReadObjectWorksSometimes
---

I have created a file using Java Bridge in objective C and am reading the file using pure Java.  Problem is that readObject() produces an IOException(), but only when the file is on a remote machine (applet reads file ok when file is on local machine, running in project builder works fine too).

Any pointers would be appreciated.



Here is a snippet.

    

// write to file using java bridge in objective C

javaFileOutputStream =  [[NSClassFromString(@"java.io.FileOutputStream") newWithSignature:@"(Ljava/lang/String;)",@"myfile"] autorelease];

javaObjectOutputStream =  [[NSClassFromString(@"java.io.ObjectOutputStream") newWithSignature:@"(Ljava/io/OutputStream;)", javaFileOutputStream] autorelease];
    
[javaObjectOutputStream  writeObject: jMap];  //jMap is a TreeMap object
[javaObjectOutputStream flush];
[javaFileOutputStream close];


// read from file using pure java

    
aURL = new URL(getDocumentBase(), "myfile");  // this works
is = new FileInputStream (aURL.openStream());  // this works
obInputStream = new ObjectInputStream(   is ); // this works

/*
following line works when run on project builder, web browser with local files but not when file is on  remove machine
*/

aTreeMap = (TreeMap) obInputStream.readObject();






thanks & regards, PeterFerrett

