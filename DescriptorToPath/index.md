---
layout: page
title: DescriptorToPath
---



What do you do when some part of your program keeps track of open file descriptors only and for whatever reason suddenly needs to access the referring file by its full path? Well, you're in luck! Such a feature is available in Darwin from **user space!** Just call fcntl on it with the F_GETPATH command like so:

    

#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {

        //new memory to hold our path; note that this is filled with 0s,
        //so you can't claim that any pointer magic is going on:

        char *newPath = calloc(4096, sizeof(char));
        int pathIntPtr = (int)newPath;

        //let's open a random file as an example

        int myFD = open("/Users/Shared", O_RDONLY, 0700);

        //we don't care about the return value here, though it should be 0:

        fcntl(myFD, F_GETPATH, pathIntPtr);


        printf("new path: %s\n", newPath);

        close(myFD);

        return 0;
}





To my knowledge, this feature is "documented" only in xnu*/bsd/kern/kern_descrip.c. It has existed at least since 10.3.0:

    

case F_GETPATH: {
		char *pathbuf;
		int len;
		extern int vn_getpath(struct vnode *vp, char *pathbuf, int *len);

		if (fp->f_type != DTYPE_VNODE)
			return (EBADF);
		vp = (struct vnode *)fp->f_data;

		len = MAXPATHLEN;
		MALLOC(pathbuf, char *, len, M_TEMP, M_WAITOK);

		error = vn_lock(vp, LK_EXCLUSIVE|LK_RETRY, p);
		if (error) {
		    FREE(pathbuf, M_TEMP);
		    break;
		}
		error = vn_getpath(vp, pathbuf, &len);
		if (error == 0)
			error = copyout((caddr_t)pathbuf, (caddr_t)uap->arg, len);
		VOP_UNLOCK(vp, 0, p);
		FREE(pathbuf, M_TEMP);
		break;
	}



(Apple engineers should blame Scrod.)

