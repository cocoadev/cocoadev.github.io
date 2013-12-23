---
layout: page
title: InstalledMemory
---

Does anyone know of an easy way to get the value for the amount of installed memory. I could just parse a log from top, but it seems like there has to be a system call you can make to get this info.

----

The sysctl() function can give you that info (and a whole lot more too), but it's usage can be a little tricky at first.  I'd advise checking out it's man page (    man 3 sysctl), the header (at     /usr/include/sys/sysctl.h) and this page which has a code snippet using it to check the number of cpus (CheckNumberOfCPUs).  -- Bo

----

Thanks Bo!!

Here's the code that worked for me:

    
#import <sys/sysctl.h>

static int MegabytesOfPhysicalMemory() {

    int mib[] = {CTL_HW, HW_PHYSMEM};
    size_t len = sizeof(int);
    int physmem;
    sysctl(mib, 2, &physmem, &len, NULL, 0);
    return physmem / (1 << 20);

}



----

Looks good, except that I think that     physmem should actually be an     unsigned long long (i.e. 64 bits), especially now that those G5s are out. ;)  -- Bo

----

Are you sure that     physmem is a 64 bit value?

this:

    
    int mib[] = {CTL_HW, HW_PHYSMEM};
    size_t len = 8;
    char eightBytes[8];
    sysctl(mib, 2, eightBytes, &len, NULL, 0);
    unsigned long long int *longlongintPtr = (unsigned long long int *)eightBytes;
    int *intPtr = (int *)eightBytes;
    NSLog(@"%i %qu", *intPtr, *longlongintPtr);


outputs this:

    
2004-02-02 19:45:32.377 Test[1149] 805306368 3458764516094219155


BTW I'm running 768 MB of physical memory (i.e. 805306368 bytes)

----

Note that sysctl returns an error value that should be checked. Otherwise, the result number that you are looking at is as good as nonsense. In addition, a quick browse through the man page says { CTL_HW, HW_PHYSMEM } is an integer. This also reveals that sysctl does not like a 64 bit value for this property name.

    
unsigned long MegabytesOfPhysicalMemory() {
    int mib[] = {CTL_HW, HW_PHYSMEM};
    unsigned long physmem;
    size_t len = sizeof(physmem);
    return (-1 != sysctl(mib, 2, &physmem, &len, NULL, 0)) ? physmem / (1 << 20) : 0;
}

// will return 0

unsigned long long MegabytesOfPhysicalMemory64() {
    int mib[] = {CTL_HW, HW_PHYSMEM};
    unsigned long long physmem;
    size_t len = sizeof(physmem);
    return (-1 != sysctl(mib, 2, &physmem, &len, NULL, 0)) ? physmem / (1 << 20) : 0;
}


----

That's interesting, but that did leave me wondering how you would accurately check the memory on a G5, since that accepts up to 8GB.  It seems there's a different key, HW_MEMSIZE, that returns the amount of ram as a 64-bit integer.  You should probably use that instead to be forward-compatible with 64-bit processors.  -- Bo

