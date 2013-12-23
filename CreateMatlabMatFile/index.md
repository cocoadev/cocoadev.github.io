---
layout: page
title: CreateMatlabMatFile
---

Create a MATLAB .MAT / .MEX file using Xcode

Several people have posted tips on the web on techniques to link to MATLAB dylibs using Xcode. The root problem is that MathWorks uses localized embedded names, and also relative path references to other linked MathWorks libraries. The libraries are in located in the /Applications/MATLAB??/bin/maci directory. In my case, I'm using MATLAB74, so what follows is most relevant for that release and creating an environment to support building a ".mat" file. However, the technique can clearly be extended to new versions, and / or .mex files.

Looking at one such library using otool:

    
]o[ otool -D libmat.dylib
libmat.dylib:
    ../../bin/maci/libmat.dylib

]o[ otool -L libmat.dylib
libmat.dylib:
    ../../bin/maci/libmat.dylib (compatibility version 0.0.0, current version 0.0.0)
    ../../bin/maci/libut.dylib (compatibility version 0.0.0, current version 0.0.0)
    ../../bin/maci/libmx.dylib (compatibility version 0.0.0, current version 0.0.0)
    /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 88.1.6)
    libhdf5.0.0.0.dylib (compatibility version 1.0.0, current version 1.0.0)
    /usr/lib/libstdc++.6.dylib (compatibility version 7.0.0, current version 7.4.0)
    /usr/lib/libgcc_s.1.dylib (compatibility version 1.0.0, current version 1.0.0)


The use of relative paths makes it impossible to get Xcode to build a command line program that will both load and run (you can get it to load with some effort). If the option is available to you, you can set DYLD_LIBRARY_PATH to /Applications/MATLAB??/bin/maci and the program will run. However, if for any reason you cannot force the caller to have set that variable, well, you are sol (as far as I can see).

Thus, I set out to find a way that I could reliably link to these libraries. However, I have no idea what might break if I made any changes to the originals, so I decided on a strategy of copying and modifiying the necessary files. What I did with success is:

1) create a new folder for headers:  /usr/local/include/matlab

2) create a new folder for libraries:  /usr/local/lib/matlab

3) created a ksh shell script to copy original files and libraries, then modify the libraries (included below). The idea is to copy the files to a "common" arena ("/usr/local/lib") where Xcode and your programs can find them

4) when you create (or modify) a Xcode project that should create .mat files for MATLAB, you would include the headers from the /usr/local/include/matlab folder, and the libraries found in /usr/local/lib/matlab, libmat.dyld and libmx.dyld.

5) with the fully qualified internal references, other libraries linked to by libmat and libmx are found.

NOTE: don't forget to modify the Library Search Path in "Edit Active Target" - if you tried to include a dyld library previously, Xcode will have modified this setting for you to look in /Applications/MATLAB??/bin/maci, and the old problems will remain. Ask me how I know this :-)

Shell script to move and modify a core group of libraries (must be run with "sudo"):

    
#!/bin/ksh

# set the folders you want here
headers="/Applications/MATLAB74/extern/include"
newheaders="/usr/local/include/matlab"
libraries="/Applications/MATLAB74/bin/maci"
newlibraries="/usr/local/lib/matlab"
path="../../bin/maci/"
newpath="${newlibraries}/"

# create the new folders if needbe (-p says do intermediates if needed)
if  ! -e $newheaders 
then
	print "Make new directory: $newheaders"
	mkdir -p $newheaders
fi
if  ! -e $newlibraries 
then
	print "Make new directory: $newlibraries"
	mkdir -p $newlibraries
fi

cp \\

	${headers}/mat.h \\

	${headers}/matrix.h \\

	${headers}/tmwtypes.h \\

	${newheaders}

cp \\

	${libraries}/libmx.dylib \\

	${libraries}/libut.dylib \\

	${libraries}/libmat.dylib \\

	${libraries}/libz.dylib.1.1.4 \\

	${libraries}/libicudata.dylib.32 \\

	${libraries}/libicuuc.dylib.32 \\

	${libraries}/libicui18n.dylib.32 \\

	${libraries}/libicuio.dylib.32 \\

	${libraries}/libhdf5.0.0.0.dylib \\

	${libraries}/icudt32l.dat \\

	$newlibraries

cd $newlibraries
for library in lib*
do
	# change internal name to use a fully qualified path
	/usr/bin/install_name_tool -id ${newpath}${library} ./${library}
	# get linked libraries names
	libs=$(otool -L $library)

	print "File: $library"
	for lib in $libs
	do
		# ksh command to strip $path from the $lib and return the result
		name=${lib#$path}
		if  $lib != $name  
		then
			print "   Changing $lib to ${newpath}${name} in file $library"
			/usr/bin/install_name_tool -change $lib ${newpath}${name} ./${library}
		fi
		name=${lib#lib}
		# if this name starts with "lib" then its a local reference, so fully quality it
		if  $lib != $name  
		then
			print "   Changing $lib to ${newpath}${lib} in file $library"
			/usr/bin/install_name_tool -change $lib ${newpath}${lib} ./${library}
		fi
	done
done


Lets look at what the modified libraries look like:

    
o[ otool -D libmat.dylib
libmat.dylib:
/usr/local/lib/matlab/libmat.dylib

]o[ otool -L libmat.dylib
libmat.dylib:
	/usr/local/lib/matlab/libmat.dylib (compatibility version 0.0.0, current version 0.0.0)
	/usr/local/lib/matlab/libut.dylib (compatibility version 0.0.0, current version 0.0.0)
	/usr/local/lib/matlab/libmx.dylib (compatibility version 0.0.0, current version 0.0.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 88.1.6)
	/usr/local/lib/matlab/libhdf5.0.0.0.dylib (compatibility version 1.0.0, current version 1.0.0)
	/usr/lib/libstdc++.6.dylib (compatibility version 7.0.0, current version 7.4.0)
	/usr/lib/libgcc_s.1.dylib (compatibility version 1.0.0, current version 1.0.0)


The list of files I included was arrived at by iteratively trying to get the program to build and run, starting with libmat and libmx. Note that the file icudt32l.dat is also needed by one of the icu libraries.

I'm posting this as arriving at this solution took some effort, and others contacted me during the process indicating that they too had spent time trying to get a solution.

Obviously the list of include files may change depending on what functions you call. This is pretty straightforward, as when you run the program, dyld will complain and the program will not run until you add the missing library (dyld tells you what library its looking for, but only one per invocation).

[This solution was suggested in a large way by http://lists.apple.com/archives/cocoa-dev/2005/Aug/msg01856.html ]

