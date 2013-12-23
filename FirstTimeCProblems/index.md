---
layout: page
title: FirstTimeCProblems
---

**Continuing string literals across lines in source code files; auto-concatenation of a series of individual string literals**

Given ObjC code like:

    
  if (![toolsController displayTool:selectedTool inView:toolsContainingView]) {
    NSBeep();
    NSAlert *alert = [NSAlert alertWithMessageText:@"Unable to display selected tool."
                      defaultButton:@"OK" alternateButton:nil otherButton:nil 
                      informativeTextWithFormat:@"A problem occurred while attempting to display the selected tool.\n\n\\

                                                  Restart the application. If the problem continues, \\

                                                  please contact ..."];
    [alert beginSheetModalForWindow:thisWindow modalDelegate:nil didEndSelector:nil contextInfo:nil];
  }


The text in the alert sheet ends up looking something like:
    
       A problem occurred while attempting to display the selected tool.

                                                   Restart the application. 
       If the problem continues,                                     please 
       contact ...


Is this normal when using a backslash to continue a line or is the compiler supposed to strip the leading blanks?

----

The proper way to split strings onto separate lines is to take advantage of the fact that C (and ObjC) will concatenate adjacent string constants. For example, writing "hello " "world" is equivalent to "hello world". So you can make your multiline string constants pretty and unsurprising like so:

    
@"This message is really long.\n\n"
@"It continues onto the next line"


----

**Do not try to use the C compiler to compile C++ code**

I am trying to learn using a book on C. A program which has 

**#include <iostream.h>**

wont compile. I get:

**firstc.c:1:22: iostream.h: No such file or directory**

----


Here's my code:

    
#include <iostream.h>

void main ()        //Editor's note: this problem is dealt with a little farther down the page
{

        float num1;
        float num2;
        float total;

        cout << "Please enter a value for the first number: ";
        cin >> num1;
        cout << "Please enter a vlaue for the second number: ";
        cin >> num2;

        total = num1 + num2;

        cout << "Your total is: " <<  total << endl;


Of course, I have tried it without the .h as well.

So the question is, when I use cc (in the terminal) or XCode to compile, why isn't this compiling. and 2., where is the iostream.h file located? Isn't it supposed to be in usr/include? And 3. If I'm missing the file, what can I do to replace it?

---

The code above is C++, not C!  Use     c++ (or     g++) to invoke the compiler from the terminal or change the file suffix to .cpp if you are using Xcode.

    iostream is buried in:     /usr/include/gcc/darwin/3.1/g++-v3 on my system (I'm still running Jaguar on this machine).
It is probably in a similar directory on yours.  3.1 might be 3.3 on Panther.

----

    iostream.h (or     iostream in newer C++ environments) is the standard I/O stream declaration for C++.  Your code
should compile with     c++ (or      g++, the standard command for invoking the GNU C++ compiler).  Be careful about
which language you are using.  If the book you are using is using     iostream, then it is not pure C, and it is bound to
create confusion.  For example, the following statements are functionally equivalent.

    
C:      printf( ""Hello world\n" );

C++: cout << "Hello world" << endl


Intermixing I/O in C and C++ can create a number of odd problems and should be avoided...

----

**Standard C now requires the main() function to return int rather than void**

I am still getting an error:

firstc.c:4: error: `main' must return `int'

What am I doing wrong this time?

----

change main to the following:

    
int main ()
{

    return 0;
}


----


Do you know what a function is? 

    int main() is declaring a function named main. The int in front of main is to declare that the return value is of type     int. This is all really fundamental stuff. I highly recommend taking a time out and reading through the first couple of chapters again. I think you have missed out on a couple of important concepts if this example is giving you problems. You will thank me months from now when you realize how awful your life could have been if you didn't hit the book sooner ;-)

See WhatToKnowAboutCFunctions for a brief summary.

----

It's also possible that he thinks, as is reasonably logical, that no function calls main, so main should return void.  That's not actually true, main returns an error code.  0 for success.  I vaguely think I remember that K&R skips over that in the beginning.

I think one of the old Borland compilers required/allowed     void main().  The book is clearly C++, given the errors encountered at the beginning.

----

*I vaguely think I remember that K&R skips over that in the beginning.*

Section 1.7, about half way through  (Page 26 in my copy): "Typically, a return value of 0 implies normal termination..." Whether that counts as "in the beginning" or not is fairly subjective I guess. Also, the specifics regarding return value are environment (i.e., operating system) specific. Hence why K&R is so vague.

*I think one of the old Borland compilers required/allowed     void main().* IIRC that wasn't uncommon in old mac programming where the program's return code was basically ignored by the OS. It's been a long time since I thought about that though, so don't quote me.

-- MikeTrent

