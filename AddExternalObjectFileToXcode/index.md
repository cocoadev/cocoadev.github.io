---
layout: page
title: AddExternalObjectFileToXcode
---

I need to add an externally-generated object file to an existing Xcode project. The object file was generated from a Fortran program (don't ask) using g77. The rest of the program is in C. Under Project Builder I just added the object file to the project and it was linked in correctly. Under Xcode the object files don't seem to be included!

As an experiment you can try this at home. Create a project for a standard tool. Compile and run the resulting HelloWorld program. Now change main.c to look like this:

    
#include <stdio.h>

extern int worldNum(void);

int main (int argc, const char * argv[]) {
	int n;
   
	n = worldNum();
    printf("Hello, World number %d!\n",n);
    return 0;
}


In the project directory use your favorite text editor to create worldNum.c. **Don't** do this with Xcode or add the file to your project!!!

    
int worldNum(void)
{
   return 3;
}


compile the program on the command line to generate worldNum.o:

    cc -c worldNum.c

Now add the object file worldNum.o to your project. Click the 'buld & run' icon and you get this message:

    
ZeroLink: unknown symbol '_worldNum'

hello_extern has exited due to signal 6 (SIGABRT).


If you look at the detailed buld results you can see that Xcode is not even trying to link against the worldNum.o object file.

Does anyone know how to make this work?

jgreenb2@ford.com

----

I had the same problem trying to use APELite with my project. I never found a way to get Xcode to automatically recognize a .o file in the project, but fortunately the workaround is very easy. Just add it to your project, then add 'whatever.o' to your Other Linker Flags in the build settings.

MikeAsh

----

I tried this and it does work. Thanks! Still seems like there should be a better way, but at least this gets me going.

jgreenb2@ford.com

----

The "better way" turns out to be adding the step of putting the object code into a library (with a .a extension) with libtool

The old (now deprecated) way of doing this consisted of 

% ar cru libfoo.a something.o another.o

% ranlib libfoo.a

