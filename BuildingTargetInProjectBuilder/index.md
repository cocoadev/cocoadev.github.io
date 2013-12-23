---
layout: page
title: BuildingTargetInProjectBuilder
---

Hi folks, I'm new to Cocoa, so I apologize for eventually dummy question. I have read Aaron's book already, created several simple apps (just for fun and exercise), but when I decided to learn something more before making serious apps I run into trouble. I started reading Bill's book "Cocoa Recipes" and everything was fine until I tried to build the application for the first time. The project name is "Vermont Recipes" and standard Cocoa Document-based Application template creates header file named "Vermont Recipes_Prefix.h", located in "Other Sources" project group. If I try to build the app, even before the compiler kicks in I get two error messages: "Missing file or directory: Vermont" and "Missing file or directory: Recipes_Prefix.h". So, I thought the space character in the project name and header file name is the problem and then I realized that all my projects had no space characters in their names. To make myself sure, I created new Cocoa Document-based Application with space character in its name, and tried to build that simple app given by the template and I got the same error messages. But I suppose there must be the way to built apps with that kind of names. I downloaded Bill's project files and actually compiled it but that project doesn't contain "Vermont Recipes_Prefix.h" file. Then I deleted the file with the same name from "Vermont Recipes" project I created but again, the same error messages appeared upon trying to compile it. This actually isn't the question of Cocoa, but Project Builder underlying tools, but nevertheless I hope some of you has the solution. I don't know what is the situation with Xcode, I'll upgrade by the end of January.

And yes, a reader of this web site suggested to bury the book mentioned, because he got lost about page 150 and couldn't get project to compile. I think he faced the same problem as I did.

Thanks, and sorry about my English!
Milke.

----

This is a known problem with ProjectBuilder; to fix it, go to the GCC Compiler Settings section in the project's target settings and put quotes (") around the filename shown in the Prefix Header field (for example, replace Vermont Recipes_Prefix.h with "Vermont Recipes_Prefix.h").  If you've deleted the file, you'll probably want to copy it in from another project as pre-compiled prefix headers speed up compile times significantly. -- Bo 

----

Thanks very much Bo, it works. As I though, the solution was pretty simple, but it's my fault not looking into Target Settings options. Maybe I wouldn't figure out to put quotes around the file name, but I certainly would know to remove Prefix Header file from GCC options (as Bill Cheeseman did). Anyway, you're right about compile times with and without pre-compiled prefix headers.

Milke.

