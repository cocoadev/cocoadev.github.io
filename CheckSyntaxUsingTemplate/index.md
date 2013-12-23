---
layout: page
title: CheckSyntaxUsingTemplate
---

Maybe somebody can advice me. I have to read in files and process them. Those files has to be checked conform a syntax.
What is the best approache to get his working, is this using NSScanner or is out there a better way? I would like to create a self contained class.

the syntax
    
info {
 [ option option_name ] {
	( file pathname [ maxsize max_size_spec ][minsize ( min_size_spec | unlimited )]
	| device devicename ( user | auth | root )
	| null );
	[ severity ( final | edit | hold | deposit | cancel | reject ) ];
	[ security [ level [ level ] ];
	[ print_log yes_or_no ];
	);]
 [ category category_name {
	option_name; [ option_name; ...];
	[ security ( user | auth | man )];
	};]
	...
};

sample file
    
...
info {
option level42 {
	file ~/folder/;
	severity edit;
	security level 3;
	printlog no;
	}
option level46 {
	null;
	severity deposit;
	security level 1;
	printlog no;
	}
category cat1 {
	medical;
	lab;
	security auth;
	}
}
...

Any pointers to information about this is welcome, thank you.
----
Since there is no reply yet, I will offer some thoughts.  The format looks simple enough with some basic key value pairs.  Interestingly, the contents inside { } look like Apple/NeXT's old standard plist format for dictionaries.   It may be relatively easy to parse most of this by breaking it into chunks parsable using +dictionaryWithContentsOfFile: and http://developer.apple.com/documentation/Cocoa/Conceptual/PropertyLists/index.html.

However, because only part of the format is compatible with property lists, the best bet, just like it is with almost every parser problem, is to use a proper parser and grammar.  See flex/bison or lex/yacc or your favorite recursive descent parser framework.  Parsing is one of the oldest and best understood programming problems.  You will find copious information on the net or relatively early in any computer science curriculum.
----
Thank you very much for your reaction. I can't use+dictionaryWithContentsOfFile: because it is only a part of the file with a greater layout. Even the showed syntax sample is a small part. I do chop the file in chunks, so I can parse/checking some of the chunks with there own syntax.


I did give myself more time to research, I will look into the parser Framework. It looks like that you have experience with parsers. Do you have any recomendation/comments of such a framework for cocoa?

Thanks in advance

