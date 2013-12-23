---
layout: page
title: CoreDataOrphanRecords
---



Core data appears to be orphaning detail records on a very simple Master->Detail type of data application. To verify what I was seeing, I built a very small, simple application as a Core Data Document based application. The model is simple:

Vehicle Object (year, make, model, nickname, etc) has a one-to-many relationship to the VehiclePicture object (picture name, imageData, sequence).

I set up the data model accordingly, including a cascading one-to-many from Vehicle to VehiclePicture, and an appropriate inverse relationship. ( by the way, this sample is only slightly more advanced than the one in the tutorial at http://developer.apple.com/cocoa/coredatatutorial/index.html )

When I run the application, and add a vehicle, then add VehiclePicture records, the records add to the data set just fine. However, when deleting the VehiclePicture objects, the only thing that appears to be happening is that the references in the Vehicle object to the VehiclePictures objects are removed. The VehiclePictures objects are remaining in the data file. 

Is this a known behavior, or have I possibly set something up wrong? Copies of my data files follow:

Original file:
    
<?xml version="1.0"?>
<!DOCTYPE database SYSTEM "file:///System/Library/DTDs/CoreData.dtd">

<database>
    <databaseInfo>
        <version>134481920</version>
        <UUID>343C8D7F-A000-42C4-8463-43FA1E5EF3FB</UUID>
        <nextObjectID>102</nextObjectID>
        <metadata></metadata>
    </databaseInfo>
    <object type="VEHICLE" id="z102">
        <attribute name="make" type="string">manufacturer</attribute>
        <attribute name="model" type="string">model name</attribute>
        <attribute name="nickname" type="string">vehicle short name</attribute>
        <attribute name="year" type="int32">2006</attribute>
        <relationship name="vehiclepictures" type="0/0" destination="VEHICLEPICTURE"></relationship>
    </object>
</database>



File with a few pictures added:
    
<?xml version="1.0"?>
<!DOCTYPE database SYSTEM "file:///System/Library/DTDs/CoreData.dtd">

<database>
    <databaseInfo>
        <version>134481920</version>
        <UUID>343C8D7F-A000-42C4-8463-43FA1E5EF3FB</UUID>
        <nextObjectID>104</nextObjectID>
        <metadata></metadata>
    </databaseInfo>
    <object type="VEHICLEPICTURE" id="z103">
        <attribute name="picturename" type="string">picture_1</attribute>
        <attribute name="sequence" type="int32">0</attribute>
        <relationship name="picturesforvehicle" type="1/1" destination="VEHICLE" idrefs="z102"></relationship>
    </object>
    <object type="VEHICLEPICTURE" id="z104">
        <attribute name="picturename" type="string">picture_2 picture name</attribute>
        <attribute name="sequence" type="int32">0</attribute>
        <relationship name="picturesforvehicle" type="1/1" destination="VEHICLE" idrefs="z102"></relationship>
    </object>
    <object type="VEHICLE" id="z102">
        <attribute name="make" type="string">manufacturer</attribute>
        <attribute name="model" type="string">model name</attribute>
        <attribute name="nickname" type="string">vehicle short name</attribute>
        <attribute name="year" type="int32">2006</attribute>
        <relationship name="vehiclepictures" type="0/0" destination="VEHICLEPICTURE" idrefs="z104 z103"></relationship>
    </object>
</database>



Data file after deleting the pictures:
    
<?xml version="1.0"?>
<!DOCTYPE database SYSTEM "file:///System/Library/DTDs/CoreData.dtd">

<database>
    <databaseInfo>
        <version>134481920</version>
        <UUID>343C8D7F-A000-42C4-8463-43FA1E5EF3FB</UUID>
        <nextObjectID>104</nextObjectID>
        <metadata></metadata>
    </databaseInfo>
    <object type="VEHICLEPICTURE" id="z103">
        <attribute name="picturename" type="string">picture_1</attribute>
        <attribute name="sequence" type="int32">0</attribute>
        <relationship name="picturesforvehicle" type="1/1" destination="VEHICLE" idrefs="z102"></relationship>
    </object>
    <object type="VEHICLEPICTURE" id="z104">
        <attribute name="picturename" type="string">picture_2 picture name</attribute>
        <attribute name="sequence" type="int32">0</attribute>
        <relationship name="picturesforvehicle" type="1/1" destination="VEHICLE" idrefs="z102"></relationship>
    </object>
    <object type="VEHICLE" id="z102">
        <attribute name="make" type="string">manufacturer</attribute>
        <attribute name="model" type="string">model name</attribute>
        <attribute name="nickname" type="string">vehicle short name</attribute>
        <attribute name="year" type="int32">2006</attribute>
        <relationship name="vehiclepictures" type="0/0" destination="VEHICLEPICTURE"></relationship>
    </object>
</database>



You'll see that the only thing different before and after the deletion is the deletion of the vehicle node's attributes for the child-records' refereces:
    
 idrefs="z104 z103"


Any clues? Thanks much.

----

Search the documentation for "delete rule". Your relationship's delete rule is most likely set to "nullify" rather than "cascade".

----

It is set to Cascade for the Vehicle -> VehiclePIcture direction. Setting the inverse relationship (Picture -> Vehicle) to nullify or cascade appears to cause no difference in behavior. Having it cascade in child->parent direction doesn't seem to make sense any way. I gotta believe I'm just missing something stupid somewhere along the line.

----

Well, yep... I was overlooking something. I felt like it had something to do with Interface Builder, and sure enough... in the Binding (apple-4) dialog under the ContentSet section, the "Deletes Objects on Remove" behavior check box was NOT clicked. Clicking this then rerunning all my tests showed proper activity. Thing is... I'm not sure why that isn't ON by default. Ah well, another mystery solved, and another hours of research, testing and fooling around with it can now be put to bed. Sheesh!

Thanks again.

