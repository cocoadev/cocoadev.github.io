---
layout: page
title: SingleObjectPrelink
---



This isn't so much of a question as something I think is worth sharing about XCode, which I haven't seen described elsewhere. I wanted to create a reusable framework which can be included in other people's applications, which includes code from a static library (say, libmysqlclient.a). But I didn't want to have people install the MySQL client libraries in addition to the reusable framework.

This is how you can achieve it, AFAIK:

1. Create your framework project.

2. Open the inspector for the Framework Target in the "Targets" smart group.

3. In the "header search paths", add the path to the include files you want to use (ie, /usr/local/mysql/include)

4. Leave the "library search paths" as $(LIBRARY_SEARCH_PATHS)

5. Mach-O Type should be "Dynamic Library"

6. I switched prebinding off - I am not sure this has any affect in Tiger anymore anyway.

7. For "Other Linker Flags" I set "-seg1addr 0xC0000000" although again I am not sure this has any affect in Tiger.

8. Check "Perform Single-Object Prelink"

9. In Prelink libraries, add the path to the libraries you want sucked into your reusable Framework.

10. Installation directory should be "@executable_path/../Frameworks"

The other stuff is the usual framework stuff: Set the headers you want included in the framework as "public" in the "Role" column for example. 
When you compile your framework, the code gets sucked out of libmysqlclient.a and into your framework, so that your framework becomes portable without any dependency on the original static library, at the cost of a larger framework (in bytes).

(To include this framework in your application, add it into the project target and add a new "Copy files" build phase with subdirectory "Frameworks" then add the framework into the build phase also.)

- DavidThorpe

