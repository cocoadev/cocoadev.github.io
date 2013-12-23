---
layout: page
title: NSTaskArguments
---



So you're trying to get NSTask working with some shell command. You've tested everything in Terminal and it works perfectly, but when you move it to Cocoa you get weird errors about malformed arguments or unsupported options. What's going on?

The UNIX shell, generally some variant of     sh, parses arguments very differently from how NSTask treats arguments. To understand how this works, let's first look at the start of any good C program:

    
int main(int argc, char **argv)


What are those parameters for? They're arguments, of course. The     argc parameter tells you how many there are, and the     argv parameter is an array of C strings which contains them. This being UNIX,     argv[0] contains by convention the name or path of your program. The rest, if any, contain the arguments passed to your program.

The shell takes a raw string that you type in and translates that string into a series of arguments by parsing it. This is done by breaking the string apart where there's whitespace. This shell command becomes these arguments:

    
/path/to/yourbin hello world
argv[0] = /path/to/yourbin
argv[1] = hello
argv[2] = world


What if you don't want two arguments? Then you need to quote the string or escape the space:

    
/path/to/yourbin 'hello world'
/path/to/yourbin hello\ world
argv[0] = /path/to/yourbin
argv[1] = hello world


NSTask operates in a different world. It's not parsing any strings. It takes an NSArray containing raw arguments. If you took the above example and translated it directly to NSTask, you'll get something you don't want:

    
[NSTask launchedTaskWithLaunchPath:@"/path/to/yourbin" arguments:[NSArray arrayWithObjects:@"'hello world'", nil]];
argv[0] = /path/to/yourbin
argv[1] = 'hello world'


Where did those single quotes come from? They're just to escape the space, they're not supposed to actually show up in the argument. Ah, but remember that it's the *shell* that does all of that, and NSTask doesn't involve a shell. There's nothing parsing these strings and modifying them, so what you pass is what you get. To get what you want, remove the quotes:

    
[NSTask launchedTaskWithLaunchPath:@"/path/to/yourbin" arguments:[NSArray arrayWithObjects:@"hello world", nil]];
argv[0] = /path/to/yourbin
argv[1] = hello world


Here's another example. You want to run the equivalent of this command:

    
/bin/ls -lF ~
argv[0] = /bin/ls
argv[1] = -lF
argv[2] = /Users/yourname


That's right,     ~ is another thing that gets parsed by the shell. It never makes it to the arguments received by the program. Now you translate that to an NSTask:

    
[NSTask launchedTaskWithLaunchPath:@"/bin/ls" arguments:[NSArray arrayWithObjects:@"-lF ~", nil]];
argv[0] = /bin/ls
argv[1] = -lF ~


Oops, those were supposed to be two arguments.     ls is going to complain about illegal options. Remember that each argument goes separately:

    
[NSTask launchedTaskWithLaunchPath:@"/bin/ls" arguments:[NSArray arrayWithObjects:@"-lF", @"~", nil]];
argv[0] = /bin/ls
argv[1] = -lF
argv[2] = ~


Closer, but it'll still fail, because ls can't find the     ~ directory. Once again the shell isn't here for us, so we have to look up the home directory ourselves:

    
[NSTask launchedTaskWithLaunchPath:@"/bin/ls" arguments:[NSArray arrayWithObjects:@"-lF", NSHomeDirectory(), nil]];
argv[0] = /bin/ls
argv[1] = -lF
argv[2] = /Users/yourname


Got it!

In conclusion, to properly pass arguments using NSTask you have to think in terms of what arguments the program you're calling expects to receive. Resist the urge to escape whitespace and put options together. Think about how the shell will split things apart and parse them, then write the arguments out as they will appear *after* the shell gets done with all of that. If you're not sure what they'll be, this handy little program will tell you:

    
#include <stdio.h>

int main(int argc, char **argv)
{
    int i;
    for(i = 0; i < argc; i++)
        printf("argc[%d] = %s\n", i, argv[i]);
    return 0;
}


Compile and run it like this:

    
$ gcc -o args args.c
$ ./args -lF ~ 'hello world'
argc[0] = ./args
argc[1] = -lF
argc[2] = /Users/yourname
argc[3] = hello world

