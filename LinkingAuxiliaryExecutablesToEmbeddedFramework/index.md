---
layout: page
title: LinkingAuxiliaryExecutablesToEmbeddedFramework
---



This topic will discuss how to link auxiliary executables to a framework that is embedded in an application. The tricky part about doing this is dealing with linker warnings caused by "missing files". Three target types will be covered here:


*Cocoa Shell Tool
*Cocoa Helper Application
*Loadable Cocoa Bundle


**Cocoa Shell Tool**

The most basic case is where a client Cocoa application spawns a server task. If the server task links against a framework that is also an embedded framework of the client application, the build/test setup must accommodate a different search path.     ld expects to see the embedded framework located at     @executable_path/../Frameworks. Xcode needs to associate the server target to an application target that embeds the framework. The easiest way to do this is to make the server target a direct dependency of the client application target.

For example, if a client application is built using a target named "App" and a server shell tool is built using a target named "Server", "Server" must be added as a direct dependency to "App" (search ADC using Xcode and "direct dependencies" for information on how to add dependencies to a target). 

"Server" is built indirectly (i.e. "Server" is built when "App" is built). As long as "App" is the active target, Xcode will know where the embedded framework is located relative to the server's executable path. "Server" can only be tested as a child process if it is linked to an embedded framework. One plus side to this is the runtime log will show both server/client logs when testing.

The final "App" target setup involves adding a copy phase with a "Destination" of "Executable" and dragging the product icon for "Server" onto the new copy phase icon. This should copy the "Server" executable into the "MacOS" folder inside the application bundle for "App". Launching the server from the application is fairly easy (see code below).

    
if (!serverTask) {
    NSBundle *mainBundle = [NSBundle mainBundle];
    NSString *serverExecPath = [mainBundle pathForAuxiliaryExecutable:@"Server"];
    serverTask = [[NSTask alloc] init];
    [serverTask setLaunchPath:serverExecPath];
    [serverTask setArguments:serverArguments];
    [serverTask launch];
}


**Cocoa Helper Application**

This case is a little more difficult because a helper application has an executable path that is independent of another Cocoa application's executable path. One way around this is to setup the helper app's target as a stand alone application. This means that the embedded framework will be copied into the helper app's "Frameworks" folder. Test the helper app independently. Test the helper app as a resource of another application (e.g. test launching the app using NSTask, NSWorkspace and/or the command open). Once it's time to package both apps for distribution, delete the "Frameworks" folders of any helper app that contains redundant copies of the embedded framework and create symbolic links to the "Frameworks" folder of the main application. 

    
cd path/to/the/contents/directory/of/helper/app/that/is/now/a/resource/of/the/main/app
if [ -d Frameworks ]; then
    rm -R Frameworks
fi
ln -s ../../../Frameworks Frameworks


**Loadable Cocoa Bundle**

For the last case (Loadable Cocoa Bundle) you can treat this case just like the "Cocoa Helper Application" case except for one important setting. In Mac OS 10.4 (Tiger) dydl was overhauled to improve performance and flexibility (see link below).

http://developer.apple.com/releasenotes/DeveloperTools/dyld.html

    @executable_path/../Frameworks now has a friend (    @loader_path/../Frameworks).     @loader_path means "relative to the image containing the LC_LOAD_DYLIB command". 

So to link a loadable bundle to an embedded framework first change the framework's installation directory to     @loader_path/../Framework and add a symbolic link to the bundle's "Contents" folder that points to the location of the Frameworks folder of the main application.

--zootbobbalu

----
For that last case, it is not clear what you are refering to. Can you explain what is such a bundle, and what kind of process would load it or start it? Depending on the circumstances, the use of @executable_path could still make sense, and be pre-Tiger compatible. --CharlesParnot

----

A **Loadable Cocoa Bundle** is a target of type Cocoa->Loadable Bundle. See NSBundle for more info. --zootbobbalu

I agree that being pre-Tiger compatible is a good thing, so making use of this feature is only for those who have a need to keep an embedded framework as self contained and private as possible. 

----
Here's another trick for linking bundles against executables which works pre-Tiger, but which *only* works if you can be sure that your bundle will definitely be loaded after all the frameworks it requires are loaded. Just get ld to weak-link your bundle against them. To do this, you follow all of zootbobbalu's instructions above (including the bit about     @executable_path/../Frameworks), except that you don't include the frameworks in the "Link against..." build phase. Instead, you need to modify the following build settings:


* Include in "Other Linker Flags":     -weak_framework [whatever_your_framework_is_called]
* Include in "Framework Search Paths": the path to the directory where your frameworks are stored. If your project includes both the bundle and the frameworks, it might be sufficient to include     $(BUILT_PRODUCTS_DIR).

