---
layout: page
title: HeaderDoc
---

Automatic HTML documentation generator for C/C++/ObjC code. Similar to javadoc, if you're from Java.

An OpenSource project from Apple to be found at:
http://developer.apple.com/darwin/projects/headerdoc/

It is used for some of the apple documentation of darwin, according to apple.

HeaderDoc is slightly less capable than AutoDoc, as it only seems to output HTML. I think there is a request filed for it to output DocBook XML, which can be translated into anything, but who knows when that gets done...

It seemed more reliable than AutoDoc last time I tried both. It's a real pain (not at all worth it for even small projects, IMHO) to switch comment styles between AutoDoc and HeaderDoc, so make the right choice at first!

----

Is anyone using HeaderDoc? I comment all my headers, classes, protocols, methods, functions, etc. with HeaderDoc documentation comments, but I don't generate any separate documentation from it. The reason is this: 1) the HeaderDoc default output is ugly and not very much more usable than reading comments in headers (imho) and 2) HeaderDoc is not easy to run and it only generates documentation for me on really good days.

My framework (IconaraDOM) consists of around 35 classes, which means that it is quite big, and some kind of automated documentation generation is badly needed, but HeaderDoc just doesn't do.

Are there any alternatives? AutoDoc died a long time ago, and I know of no other alternatives. HeaderDoc 8 seems to fix many of the problems I have had, and add lot's of new syntax, but I fear that it will be unusable because of major feature bloating. It's supposed to handle ObjectiveC, JaVA, PHPLang, and probably Brainf**k as well. I just want a clean and simple documentation generation tool for ObjectiveC, do I really have to write my own? (How hard could it be...)

--TheoHultberg/Iconara

----

Good question. I used doxygen [http://www.stack.nl/~dimitri/doxygen/index.html] to generate some documentation. It reads also comments in HeaderDoc style. Actually I used it to generate documentation for your framework. I think the result is a little bit better to read than the headerdoc one. But a real good tool for ObjectiveCee documentation would be nice. Maybe that is an idea for a new project...

--ThomasSempf

----

For me it's very important what kind of output such a tool creates. It has to be readable and clear. On the other hand, the source code documentation formatting is also an issue. I have been searching and trying several options, so here are my opinions:

Autodoc is probably the best documentation tool for objective-c as far as automatic syntax recognition goes. It automatically extracts method names and parameters and creates a "clean" html. I didn't go much into customizing the output, but the created html doesn't automatically support css, so it may be a little difficult to customize it. In source code: every method that you want to have included in the documentation needs to be declared in the @interface part, otherwise it will not be included in the output (even if it's documented); that can be a little annoying, especially if you want to have private methods (but I didn't try categories). Also I didn't find any simple ways of creating a list (like parameters) and cross-links.

Doxygen is an option, but the html output is not very readable for me. The source code documentation is similar to headerdoc.

NaturalDocs can be adjusted for objective-c. The source code formatting approach is very interesting and the output is also ok, but a bit too complex for me. Since it's based on css, it can be re-formatted. I also don't like the "project directory" (where some intermediate files and project customizations are saved) approach; I think it just clutters the project directory.

Another tool I found was robodoc. It works in a different way than other tools - it doesn't really parse source code, just the comments. After some testing I get nice results with it without being forced on duplicating information. It's also possible to group methods together.

As far as I've been testing, I like the robodoc source format/output combination most, so currently I'm using that tool. If somebody else has any good alternative please share it here.

----

I've been trying to use HeaderDoc to complete documentation for a project. I've run into a bug in the way that headerDoc treats multi-character operators in C++ and wonder if anyone else has found a solution.

Here's a simple test class:
    
/*!
    @header test
    @abstract   test
*/

/*! @class foo
	@discussion a test class
*/
class foo {
	public:
		/*! @function operator+
			@result a foo object
			@param f a foo
		*/
		foo operator+ (const foo& f) const;
		/*! @function operator +=
			@result a foo object
			@param f a foo
		*/
		foo operator+= (const foo& f);
};


The resulting docs look like this (under headerDoc 8.0) :
----

operator +

public:

foo operator += (
    const foo & f );

Parameter Descriptions

f
a foo

function result
a foo object


operator +=

public:

foo operator += (
    const foo & f );

Parameter Descriptions

f
a foo

function result
a foo object


operator+

public:

foo operator + (
    const foo & f ) const;

Parameter Descriptions

f
a foo

function result
a foo object


*What gives with the first API doc? Is there any way to make this work correctly?*

-- Jeff Greenberg (jgreenb2@ford.com)

----
The current version of HeaderDoc is 8.6, so a lot of the problems mentioned here were fixed years ago.  Unfortunately, the project page still shows 8.0 as the current version.  For a more recent version, see http://www.opensource.apple.com/darwinsource/10.5/


----

How can I take my HeaderDoc HTML documentation and add it to a project so I can option-click on methods and symbols that I have created and have ProjectBuilder open up their documentation page? I can remember seeing this discussion on cocoabuilder, but can't find it now.

----

You may want to check the projectbuilder-users mailing list.  It's also at lists.apple.com.

Try

http://lists.apple.com/archives/Headerdoc-dev/2006/Jan/msg00000.html

----

Folks might want to know that Doxygen now supports generation of Xcode docsets. See http://developer.apple.com/tools/creatingdocsetswithdoxygen.html . Also see the documentation on the GENERATE_DOCSET key in Doxygen for more. We are using this for an upcoming project.

