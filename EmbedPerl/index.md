---
layout: page
title: EmbedPerl
---



Does anyone know how to embed perl in a cocoa app?  I'm trying to find and replace certain text in a very long file (without the user even seeing the file) and I think the easiest way to do it would be to use string matching and replacing capabilities of perl.  I've been looking through the documentation for perl (perldoc perlembed) but much of it is a little over my head.  Can anyone give me an example or point me to a better way of doing this?

Thanks,

JacobMarienthal

No help from me, just a second for the information :) -- KritTer

----
Until someone with the exact answer responds...

http://developer.apple.com/internet/macosx/perl.html

At the most basic level you can write a perl script that can be executed from the command line, which can be triggered from within your Cocoa app. There is Apple Sample Code that shows how to do this and it is already discussed elsewhere (on this site I think) as well.

You may also be able to call the Perl script similar to the way you might execute an AppleScript - not sure about that though...I would peruse the URL above and Apple's AppleScript web site for clues.

JoeZobkiw

----

You can use the Perl C library. Anyway, you might also be interested in CamelBones, and ObjC<->Perl bridge, under development. See http://sourceforge.net/projects/camelbones

-- FinlayDobbie

----

I don't know how set you are on using perl but there are at least two implementations of regular expression engines out there for ObjC


* OmniFoundation contains a regex object
* MOKit http://www.lorax.com/FreeStuff/MOKit.html

-- HaRald

And don't forget the regex library! "man 3 regex". If you don't mind messing about with C API, you might as well go to the source. -- MikeTrent

----

Mike, is there any good example code for using the regex library?  The man page is a bit intimidating.  I think I tried to use MOKit before, but vaguely remember having some problems with it.  

Hmm, maybe I'll just try OmniFoundationRegularExpressions

-- StevenFrank

----

I found a nice little example (thank you Google)  from this page: http://www.codepoet.org/~markw/weber/unix/lab11/ 
(http://www.codepoet.org/~markw/weber/unix/lab11/regmatch.c)

-- JosephHeck

----

Here's a quickie CamelBones based example (same example, larger on main page).  It loads a url and parses the links into an array:

    

       NSString *urlString = @"http://www.slashdot.org/"
	int encoding = 1; // Not neccessarily the best value
	NSError *error;
	NSString *html = [NSString stringWithContentsOfURL:[NSURL URLWithString:urlString] 
              encoding:encoding error:&error];

	if ( html ) {
	       CBPerl *perlObject = [[CBPerl alloc] init];
		       [perlObject useModule: @"HTML::LinkExtor"];

	       [perlObject setValue:html forKey:@"string"];

	       [perlObject eval:@"$parser = HTML::LinkExtor->new;"];
	       [perlObject eval:@"$parser->parse($string);"];
	       [perlObject eval:@"@links = $parser->links;"];
	
	       CBPerlArray *links = [perlObject namedArray:@"links"];

              NSLog(@"%@", links);
	} else {
		NSLog(@"Load failed with error %@", [error localizedDescription]);
	}



The links aren't very complete (they don't include title attributes, for example); I'm looking into where the shortcoming is.

-- Stephen

