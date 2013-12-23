---
layout: page
title: STLport
---

STLport � http://stlport.org/

Did anyone successfully use this with GCC 3.3? I unpacked it and tried:     g++ main.cc -I/tmp/stlport but I get a dozen compiler errors (related to includes).

I was interestd in seeing if it will generate smaller code than using the standard GCC STL. Check this out:
    
// main.cc
#include <vector>

int main ()
{
   std::vector<int> v1, v2;
   for(int i = 0; i < 20; i++)
      v1.push_back(i);
   v2.insert(v2.end(), v1.begin(), v1.end());
   return 0;
}

% g++ -Os main.cc -fno-exceptions -fno-rtti && strip a.out && ls -l a.out
-rwxr-xr-x  1 duff  admin  67816 13 Feb 20:20 a.out

% g++ -Os main.cc -fno-exceptions -fno-rtti -Imystl && strip a.out && ls -l a.out
-rwxr-xr-x  1 duff  admin  9516 13 Feb 20:21 a.out

In the second line I add the     mystl include directory, which contains a minimal stl replacement I wrote myself. So why is the executable 66 KiB when mine is only 9 KiB? I think it has to do with exceptions, even though I disable them, I think they are still enabled when using (some) standard library components.

Also, I do not know if the extra 57 KiB is a one-time add-on.

**That'd be something interesting to know.  If it's one-time, then 57K is no big deal. If you get 57K included with every templated class you use, than that's Bad.**

The chunk of code seems to stem from stdlibc++ and is a one-time add-on -- if I use std::string only (and not std::vector) the executable is about the same size (67 KiB), but other parts of the standard library also add to the executable, like iostream, something as simple as     cout << "Hello world" << endl; amounts to 360 KiB.

----

In my experience, STLport is a very heavy library, and it's very challenging to get just the one class you want without inadvertently including a bizillion other (unnecessary, at least to you) classes.  This has to do with the very structured approach the STL uses for prefix / postfix / global includes.  Try running just the preprocessor (sorry... I forget exactly what the switch is) and see how much stuff gets included.  You'll probably be surprised... I certainly was!  I ended up dumping STLport in favor of writing the one or two classes I actually used.  That kept the executable size much more reasonable (down to about 1.4MB rather than 11MB!)

AndrewMiner

**    -E is the gcc switch to send preprocessed output to stdout.  You'll need to remove     -o from the command line.**

----

You can use     -M to get a list of 'dependencies' (i.e. all includes) or     -save-temps to let it keep the preprocessed file. But I do not understand your findings, *including* dozen of headers should not increase executable size, as the headers only declare classes -- first when you use these, will your executable grow.

