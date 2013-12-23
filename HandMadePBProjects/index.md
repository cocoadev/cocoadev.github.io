---
layout: page
title: HandMadePBProjects
---

PBXBuild is a powerful tool for dealing with the trinity of functions-- copy, compile, and link-- that  are required to build a project.  Using Projects, Targets, and "BuildPhase**'s", the problem of building is handled in an object-oriented way.  PBXBuild takes a build command as input, and functions by finding a ".pbproj" directory in the current directory, and the "project.pbxproj" plist in that directory.  Usually, these are created using Project Builder(as they are a record of archived objects).  However, since it's just a text file(see the PowerOfPlainText), it can be edited or even created by hand.  As it's said, "anything worth doing is worth automating", and this is no exception.  There are two existing programs(to my knowledge) that attempt this:  First is Darrell Waliser's makefile importing tool(at http://homepage.mac.com/walisser/convertmake), and second is my ObjCBrowser development environment.  I don't know what Mr. Walliser's approach is intimately, so I'll detail my own:

I have an hierarchy of objects responsible for representing the objects in a .pbxproj file, and are capable of writing themselves out in XML plist format-- this is easier than it sounds, thanks to +[NSString stringWithFormat:].  I keep this code in line with the LawOfDemeter by only allowing an object in the build system knowledge of those objects which it immediately contains.  There's a lot of delegation and indirection in the system, which is good.  Some aspects of the objects(such as number and types of build phases) are hard-coded(but not hard to modify, since it won't involve reaching into the system's guts), others are set indirectly.  Basically, the structure is like this:

    
BuildSettings keeps track of a: Project 
         which contains a: Target 
                   which contains:
                        a 'BuildPhases' which contains four BuildPhase objects with different attributes.
                        various other Target-specific information(install path, info.plist, etc).
                        a ProductReference which contains path, type, etc.

A BuildPhase contains:
      A "BuildFiles" which contains many BuildFile objects.
      A type which determines the 'isa' of the pbxproj object it represents.

A BuildFile can be a BuildFile or its subclass LinkedFramework.

a BuildFile represents two pbxproj objects: 
                 a PBXFileReference(or PBXFrameworkReference) and a
                    PBXBuildFile(I think those are the names).

A Project represents two pbxproj objects:
                  a PBXProject and a 
                  PBXGroup.


It's necessary for each object in the pbxproj to be referenced by a 24-bit hexadecimal ID.  Darrell Walliser told me how to do this by using an object's memory address, and here's how:

    
- (NSString *)uniqueIDForObject:(id)object
{
     // handle 2^30 different objects (assuming address is 4-byte aligned)
     return [NSString stringWithFormat:@"0000000000000000%.8X", (unsigned)object];
}


So, shove all this stuff together(as was done in ObjCBrowser) and pbproj files can be made.

If you want more detail, see the sources to ObjCBrowser at http://homepage.mac.com/being.

--JoeOsborn

----

In a project of mine a while ago, I had been trying to use the integer value of the address. That -uniqueIDForObject: code would have been welcome. -- RobRix

