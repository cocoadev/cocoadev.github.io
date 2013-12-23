---
layout: page
title: UmbrellaFramework
---

 

To create an umbrella framework you need at least 2 frameworks: Wrapper.framework and Sub.framework. What we want to do is make Sub a sub-framwork of the umbrella Wrapper.

----

In the Sub Framework target add the folowing to the "Other Linker Flags" : 
    
-seg1addr 0xXXXXXXXX -umbrella Wrapper

In the Wrapper Framework target add the folowing to the "Other Linker Flags" : 
    
-seg1addr 0xYYYYYYYY -sub_umbrella SUB -framework SUB

Where each 'X' and each 'Y' is a hex value between 0 and f

----

then in the Wrapper Framework target add a new 'Copy Files Build Phase' with 'Frameworks' as destination. Add Sub.framework to the Copy files list.

----

At last you need to add a 'Shell Script Build Phase' to the Wrapper target with the following code
    
cd $BUILD_DIR/Wrapper.framework/
ln -sf ./Versions/Current/Frameworks/


Now first build the Sub.framework then build the Wrapper.framework

----

by: General/SimonAndreasMenke

*changed A and B to X and Y in the -seg1addr args, I found using A and B confusing, since they were actually valid values as well. --Theo* 

Yes I thought of it too but i was in class when i was writing it so i didn't have the time to change it :-) -- Simon

----
Also, look at http://developer.apple.com/documentation/Performance/Conceptual/General/LaunchTime/Tasks/Prebinding.html for a list of suggested addresses for prebinding.     0xC0000000 to     0xEBFFFFFF seem like good choices, currently, as they are among the recommended third-party code address ranges for pre- and post-10.2 applications. --General/JediKnil

----

i've encountered some problems with the shell script. so here is the correct one:
    
cd "$CONFIGURATION_BUILD_DIR/$FULL_PRODUCT_NAME/"
ln -sf ./Versions/Current/Frameworks ./Frameworks

-- General/SimonAndreasMenke
