---
layout: page
title: HowToDetermineIfAProcessIsRunning
---



I'm writing a program where I need to find out if Apache is running.  Is there an easy way to do this using Cocoa (or carbon for that matter)? I was thinking about using an NSTask with ps, but I'm hoping there's a better way. Suggestions?

-JacobMarienthal

----
Yea, check out NSWorkspace.

GormanChristian

----
Unfortunately NSWorkspace only displays apps that have been switched on in the Dock so that does not help for apache. NSTask is the best approach I can think of at the moment. There must be an easier way to do it.

-Mat

----

Normally, you'd use the Carbon Process Manager for something like this, but it only sees Carbon and Cocoa apps (maybe Apple X11 apps as well).

**Technical Q&A   QA1123** *also deals with this
[ http://developer.apple.com/qa/qa2001/qa1123.html ] *

Unfortunately, there's no reliable way to use ps or the underlying system calls that ps uses to find out if a particular process is running. Anything can appear as a BSD process name.

For apache, what you CAN do is find out where apache's logs reside (usually /usr/local/apache/logs/). Inside that directory is a file called "httpd.pid". This contains the PID of the initial apache process. It only exists while apache is running.

----
That's a good idea.  I think I'll look into it.  What's the easiest way to check for the existence of the file then?

-JacobMarienthal

----
The easiest way in my opinion is to use NSFileManager and call 

- (BOOL)fileExistsAtPath:(NSString *)path

If the file is there, bingo, you are home free. Remember of course to open the file, get the PID, check it against PS and then do everything that follows. 

-Mat

----
Hmm, isn't there a (slight) maintainability issue here? - what if at sometime in the future or in some odd version/configuration, apache changes where the logs are, or what the log is called? Wouldn't that trip up the program? 
Is there any way to find out if an arbitrary process is running? -- MikeAmy

----
A complete process list can be obtained from BSD/POSIX via the sysctl API. See: *man 3 sysctl*.

    
#include <sys/sysctl.h>

#define countof(a) (sizeof(a)/sizeof(a[0]))

int find_processes_that_look_like_apache()
{
	int mib[3] = { CTL_KERN, KERN_PROC, KERN_PROC_ALL };
	
	size_t count;
	
	int err;
	
	struct kinfo_proc *kp = NULL;
	
	do
	{
		if (NULL != kp)
		{
			free(kp);
			kp = NULL;
		}
		
		//
		// The following call gets a recommended buffer size for an actual,
		// like call, to maybe succeed.
		//
		
		err = sysctl(mib, countof(mib), NULL, &count, NULL, 0);
		if (-1 == err)
			break;
		
		kp = malloc(count);
		if (NULL == kp)
			break;

		//
		// Note:
		//
		// On the following call, an errno ENOMEM value signals that more data
		// was available than was able to fit in the buffer we supplied.
		//
		// This will mean that the process list has grown between our call
		// above and the following call. For demo purposes we retry the
		// operation until we have a full snapshot of the process list.
		//
		// In production code, some sort of perinoid repeat governor should
		// be in place to guard against the posibility of an infinite loop.
		//
		
		err = sysctl(mib, countof(mib), kp, &count, NULL, 0);
		if (-1 == err && ENOMEM != errno)
		{
			free(kp);
			kp = NULL;
			break;
		}

	} while (-1 == err);
	
	if (NULL != kp)
	{
		const int max = count / sizeof(struct kinfo_proc);
		for (int i = 0; i < max; ++i) {
			if (0 == strcmp(kp[i].kp_proc.p_comm, "httpd"))
				printf("Something that appears to be Apache seen at pid %u\n", kp[i].kp_proc.p_pid);
		}
		
		free(kp);
	}
	
	return err;
}


----

Here�s a neat Unix trick I like: call <code>kill(pid, 0)</code>; if the return value is 0, the process is still running.  If the return value is -1 with an <code>errno</code> value of <code>ESRCH</code>, the process is not running.  If the return value is -1 with any other value of <code>errno</code>, then something wonky has happened.

�SethKingsley

