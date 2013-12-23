---
layout: page
title: FindingUptime
---

&I've been trying to find out how to get the proper uptime tonite, this is what i have so far:

    
- (long)runtime {
	long boottime, currenttime = time(NULL), runtime;
	size_t size = sizeof(long);
	sysctlbyname("kern.boottime", &boottime, ;size, NULL, 0); 
	runtime = currenttime - boottime;
	return runtime;
}

- (NSString *)uptime {
	long microseconds = [self runtime];
	double seconds = microseconds/100;
	double minutes = seconds/60;
	double hours = minutes/60;
	return [NSString stringWithFormat:@"%lf",hours];
}


However, when ran it logs this:

    
[Session started at 2004-11-23 20:59:27 -0600.]
2004-11-23 20:59:29.170 Inspector[1469] 22.937500


However the terminal reports different:

    
Dan-Sauls-Computer:~ dan$ uptime
20:59  up 1 day, 11 mins, 2 users, load averages: 1.66 1.28 1.06


Any ideas?

----

Why not just run NSTask with uptime, and parse the results?

----

I was originally doing that (actually popen()), but I would rather not rely on another program.

----

Does this machine sleep? Maybe kern.boottime is reset upon waking.

----

The sleep comment is dead on. I think the function you want is called UpTime() which returns an AbsoluteTime structure, and it stops while sleeping. There's a nice discussion of AbsoluteTime (struct) as well as Duration (32 bits) here:
http://developer.apple.com/documentation/Hardware/DeviceManagers/pci_srvcs/pci_cards_drivers/PCI_BOOK.197.html

And how to convert between data types here:
http://developer.apple.com/documentation/Hardware/DeviceManagers/pci_srvcs/pci_cards_drivers/PCI_BOOK.19a.html

If you use Duration (I did) the trick there is to note that if the number returned is negative, its in microseconds, if its positive its in milliseconds. Its so very easy to screw up.

----

Did you check back with sysctlbyname(3)'s man page?

KERN_BOOTTIME
A *struct timeval* structure is returned. This structure contains the time that the system was booted.

----

Try this:

    
#include <time.h>
#include <errno.h>
#include <sys/sysctl.h>

[...]

/*
 * Returns how long (in seconds) the system has been up.
 *
 * Returns -1 on failure.
 *
 * This function reads the kern.boottime sysctl value and
 * subtracts it from the current time.
 */
+ (NSTimeInterval)uptime {
	struct timeval boottime;
	size_t len = sizeof(boottime);
	int mib[2] = { CTL_KERN, KERN_BOOTTIME };
	if (sysctl(mib, 2, &boottime, &len, NULL, 0) == -1) {
		perror("sysctl");
		return (NSTimeInterval) -1;
	}		
	time_t bsec = boottime.tv_sec, csec = time(NULL);
	return (NSTimeInterval) difftime(csec, bsec);
}

