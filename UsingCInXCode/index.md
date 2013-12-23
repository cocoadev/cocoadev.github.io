---
layout: page
title: UsingCInXCode
---



I tried the following code in Xcode. The problem is that the printf statements print out AFTER the scanf function asks for a response.

    
#include <stdio.h>

int main (int argc, const char * argv[]) { 
    // insert code here... 
    char a; 
    printf("Type in a character \n"); 
    scanf("%c", &a); 
    printf("The character you typed was %c\n", a); 
    printf("Type in another character: \n"); 
    scanf("%c", &a); 
    printf("Thank you for your cooperation. You typed %c the second time.\n", &a); 
    return 0; 
}


What can I do to get printf and scanf to work properly in Xcode? This was created as a Standard Tool. --General/AlexanderD

Try putting in a     fflush after     printf, before     scanf.  Specifically,     fflush( stdout ).  This is sometimes
required on Unix systems.

That worked!  Thank you! --General/AlexanderD

It seems like inputing characters only works if you put a space between the quote and the percent sign, such as
    
scanf(" %c",a); // NOT scanf("%c",a);


If I try to use strings such as in the code below, I need to type in a fflush(stdout) before every scanf.  This is annoying.  Also, if I type in more than one word separated by spaces, the second scanf function does not work.  All the rest of the printf functions print out and the last one prints out the second word from the first scanf.  What to do?

    
    char a[20],b[20];
    
    printf("Please enter a word:");
    fflush(stdout);
    scanf("%s",a);
    printf("Your word was %s.\n",a);
    //fflush(stdin);
    printf("Please enter another word:");
    fflush(stdout);
    scanf("%s",b);
    printf("Your second word was %s.\n",b);


----

This behavior is expected and is specified by the C stdio library. You can do a **man scanf** for a complete description for scanf input behavior. Also, while I don't have an online link on hand, you may also want to refer to some documentation that explains C runtime streams in more detail. A **man stdio** will get you started.

I read the "man stdio" and it does not seem to address my question.  Even when I fflush the stdout and stdin, the second scanf and printf functions just print out without collecting input a second time.  See what I mean below.  Here is some code that I got off the internet that should work.  It did not have the fflush(stdout); line, so I added it.  --General/AlexanderD
    
    int n;
    char string[80];
    for ( n=0 ; n<2 ; n++ )
    {
        printf( "Enter some words: " );
        fflush (stdout);                          // I added this line
        scanf( "%s", string );
        printf( "The first word you entered is : %s\n", string );
        fflush ( stdin );
    }


Here is the output...

    
Enter some words: This is a word	// After I hit return the next two
					// lines come up automatically, without
					// giving me the chance for entering the
					// second string.
The first word you entered is : This
Enter some words: The first word you entered is : is


----
Here is some clarification.  I basically want to be able to put some text on the screen (hence the use of printf) and get some input (hence scanf), then put some text on the screen, then get input, etc.  Imagine one of the early text adventure games.   So how do I do this in C using General/XCode?  The funny thing is that I never had a problem doing this using previous compilers (namely General/ThinkC).  Textbook C should compile in General/XCode, right?  What is going wrong? --General/AlexanderD
----
Well, it's not that Textbook C is not compiling with Xcode, it may be a mater of behavior with Xcode's piping of the processes I/O outside of a *real* interactive terminal. I am going to play with it for a bit see if Xcode is causing these functions to behave differently as it were. With that said, at first glance, it would appear scanf is behaving per the standard. In addition, While I have not had allot of experience with General/ThinkC, I would be wary of using it's behavior to judge what should happen as per the standard.

----

I'm curious why you're using Xcode at all for this? You could do this just as well in vim, General/TextEdit, General/BBEdit, whatever.

**Note:** *Why not use Xcode? The issue he is having has nothing to do with the development environment. Besides, I don't remember General/TextEdit having very good Syntax highlighting or interactive debugging features*

**Note:** *Why trust Xcode to do the Terminal's job?*

**Note:** *If you bothered reading, You would have known that Xcode's process piping had nothing to do with it. And please do tell, how is vim (masochistic), General/BBEdit (expensive), or General/TextEdit (not applicable) going to be any more trustworthy in this respect?*

----

I did take a quick look and it behaves as I expected. A couple of notes that can be gleaned from the man pages I mentioned before.

You should specify a maximum length specifier in your format string for scanf. In your cited case it should be more like "%79s".
To obtain a string that includes spaces, use something like "%79[^\n]".

After some more testing, I do see what you mean regarding the subsequent
scanf calls returning immediately. Even with my suggested formats, it behaves
this way, both in Xcode and in a terminal. I suspect this is scanf taking the remaining
newline as more input. You can get around this by modifying the [accept/restrict] set.
As a side, *fgets* seems to get the behavior you are after.
----
Thank you.  I'll need to try this out.  --General/AlexanderD
----
I tried out fgetc and fgets and they seem to give me what I am after.  The only question I have now is that fgets stores the return key that I use to enter the string as the last character of the string.  This means when I print the string using printf, it puts in a return right after my string.  Does anyone know how to take off the last return of the string that fgets stores?  Thank you for all your help so far. --General/AlexanderD
----
There is a thousand ways to achieve this but these spring to mind.

Use strsep

Use strtok or strtok_r

Do something like the following...
    
		char *p = strchr(input_string, '\n');
		if (p) *p = '\0';

You may want to consider reading the man pages more often or getting a good C runtime reference.

----

Finally, I'll add old C wonks have recommended avoiding scanf for years because of problems like these. Instead, use fscanf, gets, or some other function ... -- General/MikeTrent

----

gets should be avoided, always prefer fgets.

----

There is a difference between running your program from the command line and running within General/XCode or Project Builder or any other tool. You might want to set some environment variables if you want to run directly from your General/XCode.

But the real question is - why are you doing this? are you trying to learn how to use scanf or what? This is not the way to interact with a user, specially on a Mac. Create proper user interface and use General/TextField for data entry. This will be much more productive.

----

The why is obvious in this case, developer convenience. Especially obvious considering he opted for a method other than scanf. It had nothing to do with Xcode and in this case the interaction with Xcode and a *real* terminal is identical, scanf behavior was the issue. I would not equate Xcode's capabilities to PB's.

Also, you would have to take into account that perhaps he is developing a CLI tool. People are developing applications on OS X that are deployed on other UNIX platforms (without an General/NSTextField).

**Note:** stdlib considered harmful.

----

My apologies in advance if this post is in an inappropriate place.
Being a TOTAL NEWBIE, I have been ovewhelmed by the sheer amount of documentation available on the web - and provided by Apple. While the docs are, indeed, very well written, I have found that most of the tutorials or introductions pre-suppose a certain level of knowledge from the reader. Cocoa tutorials often suppose a certain level of familiarity with Objective-C, and Objective-C pre-supposes you are totally conversant in C. While, like most users, I tried jumping right at the deep end, and following many of the good tutorials on the web, I found after a little while that the amount of BASIC, foundation knowledge that we, beginners, end up lacking, makes us more prone to acquire bad habits, mis-use the tools and the framework, and generally waste A LOT of time. Therefore, I thought that perhaps in my case, the wisest thing to do might be to start by reviewing some basic ANSI C syntax (which I haven't looked at for, like, 8 years), to then review the full docs on Objective-C, to then dive into the Cocoa frameworks.

So, I've dug out my trusty old copy of Kernighan & Ritchie's "The C Programming Language", and am ready to start typing away some (*gasp*) little procedural programs, when I stumble across my first problem: how can I write a non-graphical, procedural program, in C, in General/MacOS X?

General/XCode docs tell me that it can compile C code, and that I should be able to produce a command-line program with it, but there is no documentation for it! I suppose this might *really* be too basic a thing to do with General/XCode, and they might not expect anyone to use it for this type of exercise. If that is the case, should I be using a command-line tool to do this, instead of General/XCode?

If there is anyone out there able to provide me with some step-by-step instructions, I would be eternally grateful! Any guidance would be GREATLY appreciated! 
----
To create a straight C command-line program in Xcode, Select **File** / **New Project**, Scroll to the end of the project types list and pick **Standard Tool**.

To compile a simple, single file C program from the command line, something like the following should get you started:

**gcc -O0 -g mysourcefile.c -o myprogram**

To compile a release version of your code you can replace the *-O0 -g* with *-Os* or whatever optimization level suits your fancy. There is a ton of GCC documentation on both Apple's developer site as well as other places on the web, most notably the GCC sites.

----

The scanf() documentation (man scanf) is very clear on this problem. Using a %s will break at white space. Instead, use %[n]c, where n is the field width and c is a pointer to a character which is the beginning of an array at least as big as n (probably exactly n+1 with the last character being '\0'). � General/BrentGulanowski
