---
layout: page
title: CompilerTricksAndTips
---

The folks in the AIM room Cocodev and I got to talking, and we realized something. Apple's included GCC has the C99 standards turned off. The C99 standards make C a lot more bearable, adding support for a few C++ features we all like including:


* Variable declaration anywhere inside a method, not just at the top.
* <code>for</code> and <code>while</code> loop iterator variables (in the loops scope!).
* Using <code>goto</code> to jump to anywhere in the method, even after variable declarations (unlike C++).


These features are pretty nice (there are more, these are just the big ones) and easy to add. You simply need to add the compiler flag: 
<code>-std=c99</code>

I'd do it in your target settings under the "other flags" option. With this option, this block of code compiles without warning.

    
int main() {
	printf( "Beginning my example!\n" );
	int x = 0;
	for( int x = 0; x < 100 ; x++ ) 
	{
		// Do something 100 times, the x is the INNER x.
	}
	int z = 55;
	printf( "%d", z );
	return x; // return 0
}


I think the extra 15 seconds it takes to add this option to your target settings is far outweighed by the convenience of these features.

-- DaveFayram
----
Ok, I have a question. I was using gcc3.1 for a Cocoa program, and I had a method that did this, and only this:
    
for(int i = 0; i < MATRIX_SIZE; ++i)
{
   for(int j = 0; j < MATRIX_SIZE; ++j)
   {
       // DO STUFF
   }
}

Without -std=c99 it gave me an error that said "`for' loop initial declaration used outside C99 mode" so I added the -std=c99 flag, and it said I couldn't import the Cocoa headers, or any of them! What's going on here? I tried taking the declarations of i and j out of the for loop line without C99 on, and that worked fine, but I've always been able to have it in there before! What's going on?

----

Wow. Apple kinda made a mistake here. We can see the problem with precompiled headers right here. Clearly, the __STDC_VERSION__ macro is what's causing the problem. However on my machine these are just warnings and the machine falls back gracefully to the standard headers. I don't know why it'd be breaking on your machine. On mine it just gives many warnings.

What we need to do is write to Apple and ask them to please fix this little problem. I can't think of many reasons we need to be pigeonholed to using the old standard, can you?

-- DaveFayram

----
Ok, yeah, my computer did just spit out warnings, but with the standard headers, it took 4-5 times as long to compile the files and work. I tried this both in PB and the CLI and I got the same warnings for each as you described above.

I agree, we need to write Apple about this (but how?).

BTW, is there any way for Project Builder to include this tag in every project it makes by default? That would be most useful once this flag starts to work correctly.

----

You can copy /Developer/ProjectBuilder Extras/File Templates to ~/Developer/ProjectBuilder Extras/File Templates and then make changes in there; you have to do it to each project template, but hopefully never again :)

I'm not entirely sure whether I'm for or against this. I tend to like to declare variables at the top of a scope anyhow because it makes things a little more readable; if I'm reading at the middle and see a variable I'm not sure of the definition/type of, it's usually easy to find it. And I *know* that goto isn't something I'll be using :|

Still and all, it'd be nice if it didn't have to recompile all the headers for those of you that want to make use of this. Is there any way you could make your own sets of precompiled headers? -- RobRix

----

Well Rob, don't be too sure about that lack of goto thing. After all, you might need to break out of multiple nested loops. It's nice to know your goto logic will hold firm without any hidden C++-style consequences. 

As for the variable declaration, it's all a matter of style and circumstance. Personally, I think the loop declaration thing is rather nice, it certainly helps when trying to avoid accidental name collisions, using x as a loop counter twice in a big function being an example.  Also makes your code easier to maintain. 

The problem with this include is that <code>__STDC_VERSION__</code> is defined. Undefining it allows you to use the compiled headers AND use the C99 spec, but it's a pain to undefine that thing in each source file (-U doesn't seem to work).  The C99 spec is just so much nicer, we should all use it! (flamejihaddon'targueflamejihad). :)

-- DaveFayram

----

Sorry Dave, still certain about avoiding goto :) Generally, my function/method design is to keep things short and sweet; I have never needed to break out of multiple nested loops without being able to just return. I guess this is because I try to emulate mathematical functions where I can. Just my style. Out of curiosity, what are the C++ complications there? I did a small amount of C++ coding before I moved over to ObjC, but I never used goto there, either :)

I'll definitely agree about the variable declarations for loops bit. That is one thing I would find useful, and might even use :)

If you've got a header or something that you include just about everywhere (sorta the way you'll have <Cocoa/Cocoa.h> or <Foundation/Foundation.h> included), you could always put the undefine in there. But there's got to be some reason why -U isn't working...

I'm also curious about what the other changes in C99 are... know of a good comparison reference? A few quick google searches didn't turn anything up, but I'm notoriously bad with google.

Personally, I still find that goto and willy-nilly (i.e. non-loop-specific) variable declarations make code less readable; I'd consider them "bad coding practices," generally. But this is deep into IMHO territory. -- RobRix

----

Yeah, well.. some people like them, some people don't. One thing I think that does help is to put one-shot variables near where they are used. Loop variables are a limited subset of these, of course. However, if you're going to make a temporary variable, no need to clutter real-estate at the top of the funciton that otherwise would be used for important declarations.

GOTO is one of those things that I think most people have a violent, and mostly unwarrented reaction to. Indeed, GOTOs can be used to make code that inspires voiolence, but they can also be used to greatly simplify code. I've seen times when many different points in the code need to do one thing, then exit the function. Calling a sub-function wasn't an option because of the very large objects already on the stack (for performance, but then this wasn't a conventional box). A single labeled statement ERROR_FAIL made the code a breeze to maintain, and I thanked the writer immensely.

I personally have had a hard time finding a good comparison between the two standards. I know C99 has stuff I like, besides this. For instance, its inline support actually approaches something that makes sense.

-- DaveFayram

----

Fair enough :) I suppose I just had to deal with far too much evil-goto use in my classes. Maintaining Mike Ens' code was definitely the violence-inspiring kind of activity :) -- RobRix

----

Here are two pages describing the differences between the old and the new standard:

http://wwwold.dkuug.dk/JTC1/SC22/WG14/www/newinc9x.htm

http://home.tiscalinet.ch/t_wolf/tw/c/c9x_changes.html

-- Gabbe

----

Coming back to the subject of enabling C99, I've added the     -std=c99 bit to my target in order to have the use of variadic macros, but my compiler is dragging its heels:

    FFNeuron.m:47:57: warning: __VA_ARGS__ can only appear in the expansion of a C99 variadic macro
FFNeuron.m:47: `__VA_ARGS__' undeclared (first use in this function)
FFNeuron.m:47: (Each undeclared identifier is reported only once
FFNeuron.m:47: for each function it appears in.)

Any ideas?

-- RobRix

Okay, I think I've solved that one for myself. I now use     #define FFA(__objects__...)  [NSArray arrayWithObjects: __objects__ ] which is working just fine. I don't even have to turn on C99 mode.

-- RobRix

----
Regardless of personal style, enabling these standards can help when porting code from other sources.

--Paul

