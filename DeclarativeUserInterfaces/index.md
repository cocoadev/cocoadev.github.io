---
layout: page
title: DeclarativeUserInterfaces
---



I had a long discussion today about declarative user interfaces with a fellow hacker. We started off in web applications and why Model2 sucks, and tried to find alternatives (like Model2X), and discussed why they suck too. Then we started arguing about HTML and declarative user interfaces, it was a very interesting discussion, and I'd like to continue it here.

It seems that we are moving away from the traditional code-generated user interfaces and into the era of declarative user interface languages. Mozilla XUL and MS XAML are two examples. HTML is another, more widely used. Web applications are becomming more and more common and their user interfaces are all based on HTML. Even the iTMS GUI is some kind of XML. 

Now, what should we make of this? I'm quite sceptical, to me it looks like a step backwards, but many seem to like it. I love the way IB works (drag, drop and serialize), it's much bettern than having to code the UI yourself (the Java-way) and it's so much better than code-generating UI builder tools.

I mostly program web applications for a living, and I really hate HTML. You could say that it's unfair to judge all declarative user interface languages on the basis that HTML sucks, and you would be right, but I really have a problem with user interfaces that are generated. With HTML you have no idea of how the result will look until it's sent to the client (if you're not creating static web pages, but who does that anymore?). But I'm rambling.

What are your opinions on this topic? Should we ditch Nibs and move to XAML? Should we declare holy war on declarative user interface languages? Is it important?

--TheoHultberg/Iconara

----

By creating web pages for a living, I can agree with you completely that HTML is a pain to work with. It was never originally designed to create graphically intensive web pages with complicated layouts. Todays demands for web pages are difficult to satisfy with plain HTML, and anything beyond that you risk losing customers due to incompatibility with their setup. Even if you stay within the limits of the HTML language, the page may display completely differently depending upon the client's browser and operating system -- ridiculous.

I personally wish the web standard was closer to Flash. Where it is possible to get pixel perfect precision, and the use of vectors instead of images can decrease loading time. Although I'm not the biggest fan of Flash, and I try to stay away from it because many people still do not have the plugin, if the web standard was somewhat of a mix between Flash and HTML, I would be in heaven.

I love Interface Builder and Nibs. It makes a lot of sense to define the interface by "drawing" it onto the screen instead of defining the location of each control programatically. Ironically though, I hate WYSIWYG editors for HTML, primarily because it is often the opposite (what you see is **not** what you get) and all it does is generate code - which I prefer to have more control over. Nibs on the other hand, are a resource and all code is separate from it. It also works in different programming environments/languages.

-- RyanBates

----

I read up on XAML a little, in particular, Introducing Longhorn for Developers:

http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dnintlong/html/longhornintrotoc.asp

"I know! Let's bring the brilliant and easy-to-use web development process to application development!" ... I am **extremely** glad Apple did not take this approach with OS X. The last thing we need is for every application to look like a web page. I realize it will probably not go that far, and yes, XAML does have many advantages over HTML, but I can't see it taking off for designing the UI for feature complete applications.  * -- actually, I disagree... we **do** have apps looking like webpages... ever launch a java app on your shiny OS X box?*

The primary problem is that too many details are lost in a markup language. If an attribute isn't specified, the default will be used, and I have no doubt the default will change depending upon the environment (can anyone say "themes" or "OS updates"?). On the other hand, the lack of detail can be used to an advantage if they are careful enough: by providing a consistent user interface. However, when attributes are then specified, the consistency is lost. I think the bad outweighs the good in this situation.

Apple's Nibs do not have this problem. All attributes are specified so the attributes cannot change depending upon the environment (except for when themes mess around with the graphic files). This gives the developer much more control over how the resulting application will look.

There are some important advantages that come from a user interface being generated from text. Most notably, it is far easier to generate, edit, and parse text than a specific binary resource file. In Apple's case, you are pretty much stuck with Interface Builder. This problem is noticeable when creating a project and the menus say "NewApplication" instead of the name of the application. Perhaps Apple will improve the script-ability of Interface Builder to eliminate this problem.

In short, I cannot understand Microsoft's intentions in combining web development with application development. The two mediums serve entirely different purposes. Generally, applications are utilities which interact closely with the user, and websites are for displaying content. Can anyone imagine a website attempting to mimic the functionality of Photoshop?

I realize this viewpoint is a little premature, especially since XAML isn't even finalized and I haven't had a chance to see it in practice, but it's just my opinion from what I've seen so far. It may change once I get to see XAML in action, but so far I'm not impressed. What are some other opinions out there?

Sorry for targeting XAML directly with this long rant, and it may not really belong on this site, but XAML is easier to compare to Apple's Nib approach than HTML is.

-- RyanBates

----

One thing you can say about XAML is that with it the human interface guidelines (if M$ ever had those) goes out the door. The problem with webpages as application interfaces is that they are not standardised a bit. Buttons look differently (sometimes like Windows widgets, sometimes like Mac widgets, somtimes an image, sometimes a link), menus are done differently, navigation is done differently, and so on. With desktop applications, even Windows applications have a common look&feel. Webpages don't, and I fear that XAML-based applications won't either. They will look more like powerpoint presentations than applications.

--TheoHultberg/Iconara

----

Regarding the attributes missing when using a markup language, XML Schemes do allow you to specify the default value of attributes, meaning that you'd need to change the XSD, not much different than how e.g. NSView's "hidden" attribute is currently NO by default, but Apple could change it ;)

Personally I am a bit skewed toward declarative interfaces. Something like MUI (MagicUserInterface for AmigaOS) had macros to instantiate objects, each object took a "tag list" (varargs) of the attributes, so all in all it would look something like:
    
win = WindowObject,
   Child, VertGroup,
      Child, TextObject,
         MUIA_Text_Contents, "Do you want to continue?",
         End,
      Child, HorizGroup,
         Child, RectangleObject, End,
         Child, SimpleButton("No"),
         Child, SimpleButton("Continue"),
      End,
   End,
End;


This is a window containing the text "Do you want to continue?" and two buttons below (with spacing to the left of these).

The system had auto-layout, you could tweak this using weights, fixing the sizes (also fixing the sizes to the width/height of given strings etc.). So this was never a problem, and it meant all user interfaces created with MUI were fully resizable.

Since the above is just C macros, e.g.     WindowObject expands to     NewObject(MUIC_Window,  and     End expands to     TAG_DONE), so with a DSL it could probably be made to look nicer, although embedding it in C had the advantage of allowing plain C, e.g.     SimpleButton is a C function which returns a button object with the title given as argument etc.

All in all I was quite fond of this concept -- the major advantage was that cut'n'paste was so much easier than fiddling around in a graphical "editor" and batch-editing (search'n'replace) was also quite easy -- e.g. try to change your applications name in IB, how many places does that phrase appear?

It also had the advantage of "full disclosure", e.g. if you get a Nib from another, or if you haven't worked on it in a long time, a lot of things can really be hidden (bindings, outlet or target/action connections, data formatters etc.).

I would hate for the above to be XML, but I really do miss the PowerOfPlaintext with IB for two reasons, one being that I do a lot of new projects, and I hate to always have to wire up the basic stuff in IB to start my new project each time (and even having to start IB) and secondly because I also do a lot of rapid prototyping, and the amount of GUI which I can re-use from previous prototypes is very limited, almost only self-contained Nibs will work, and even here there may be some problems when changing the type of File's Owner (this breaks my bindings, I need to re-connect action methods and sometimes there just appears "corruption" when doing this, requiring me to try and reset everything, which isn't really easy in IB).

----

I just read something scary about XAML: if you use VS.NET, which probably works similar to IB, and save, VS.NET *generates* XAML-code. Later, when you build your project, the XAML is used to *generate* C# and VB code (depending on project type, apparently). This code is then compiled into bytecode. This means that between what you see in VS.NET to what you see in your application the representation has been transformed twice. Anyone who's messed around with code-generating tools, and in particular code-generating UI-tools knows that this spells trouble.

In comparison, what IB does is, more or less, that it creates a real UI, with real objects, then serialises the whole object graph into a nib. When the nib is loaded the objects are recreated, and the result is the *same* object graph. No transformations, nothing is lost, nothing is added. Perhaps this is not exactly what IB does, but in general this is how it works.

--TheoHultberg/Iconara

----

Well, the last step, turning C# into byte code, is no different from compilation of ObjectiveC into PowerPC instructions.  And generating markup is IMHO rather different from generating code, because the intent of the markup can easily be machine-determined, which is not so with auto-generated code, and thus it should not give rise to problems to further transform the XAML "code"...

But where did you read about this? it seems rather awkward to me, why not write it in XAML and keep it in XAML (of cause attaching event handlers etc. will require either embedded code or linking to external code)?

----

I read it in an article on O'ReillyNet:
http://www.ondotnet.com/pub/a/dotnet/2004/01/19/longhorn.html.

Quote:
*XAML files are usually compiled rather than being parsed at runtime (although runtime parsing is available it you really need it.) When you build a XAML-based project, the XAML compiler generates a class for each XAML file. These classes contain code to create the objects specified in the XAML. If you are curious, it is possible to find this generated code.*

Seems really stupid to me.

Of course C# to bytecode is no big deal, the point is that widgets are transformed into XAML, which is transformed into C#. Bytecode is ok, even if it's better if it can be avoided (as in the case of IB).

--TheoHultberg/Iconara

*Seems that XAML is really just for interchange and not much different than if Apple would publish the format of Nib files. The keyed format could just as well have been some form of XML. One advantage with XAML over Nibs are that they can be generated on-the-fly, AFAIK there is no way we can construct a Nib file in memory and let the Nib loader (NSBundle extension) parse it and create a GUI from it.*

It's a big difference between serialized objects and code generation. Serialized object files will not be *interpreted*. Of course, you could mimic a serialized object and call it whatever you want, and I can hack bytecode and call myself a compiler. Let me once again explain that when a Nib-file is loaded, the object graph will be reconstructed, and the result will be *the same* as it was before the Nib was saved. That's not what's happening in the case of XAML, or whatever. Yes, it's similar, but there's a difference. 

And, in my oppinion, to be able to generate the UI on-the-fly is a problem, not an advantage. As I mentioned above, one of the reasons why I hate the web is that it's generated. I want UI:s to be static boxes with a fixed number of outlets and actions. If that is fulfilled, I belive that we can move on and create usable applications. If we have to handle UI:s that we don't know *anything* about until runtime, too much time is spent trying to get the damn thing to work, and too little time on making it work the way it should. Too many possibilities is not a good thing.

--TheoHultberg/Iconara

----
Sorry but I just have to jump in. I think the whole notion of "declarative" interfaces is fundamentally flawed. Ultimately you have to write code to "interpret" the declaration and in so doing you open the door WIDE for both ambiguity and errors to creep in between the "declaration" and the "rendering" (aka. implementation, aka "imperative" form). You also introduce a delay from specification to implementation that can vary greatly (as we have seen in the HTML world) between implementations.

Before HTML there was TeX (if you're old enough to remember). TeX is a "programming" language with a small, stable set of rendering instructions upon which more complex operations (aka "user defined functions") could be built. As a result of its nature, it has not need to be changed in a long time; nice and stable and predictable.

There is also another "layout" language, lout, that is built on only about a dozen or so primitives. (see http://snark.ptc.spbu.ru/~uwe/lout/lout.html). While these languages (TeX and lout) lack "interactive" features (aka buttons, links, fields, etc.) I should hope the point is clear; imperative forms win over declarative forms in robustness.

Let's not be afraid of "creating UI language". I'm just suggesting that we shouldn't build in unnecessary limitations.

-jason

----

Old enough? Every student of natural science that I know of use or used LaTeX for all their reports. LaTeX is a set of pre-made functions, so that the student do not have to program their own, and hence just use LaTeX as a MarkupLanguage, contrary to what you seem to imply.

Also, the thing about different renderings, HTML is probably the worst example, because it was not meant for giving the same visual result, on the contrary. But PDF is, and that has been quite a success.

So I don't really buy your arguments, on the contrary, declarative languages do have power over coding your own stuff. Would you rather program your own database query? or declare it in SQL? Would you prefer to code your own email validation javascript, or would you rather like to declare that the datatype for this form element is an email? etc.

With code comes bugs, and with declarations you state intent.

----

True, declarative languages are not all bad. They are basically a higher level programming language; but in this case, the flaws start showing up when using them to declare user interfaces, which brings up an important distinction: there are many more variabilities to consider visually then there are in something like an SQL statement.

Compared to visual displays, SQL is fairly simple. There aren't many holes in the language for the translator to fill in. You can know exactly what type of results you will get when defining an SQL statement.

When applying this to user interfaces, there are far too many possibilities. You have color, placement, size, etc. which must be accounted for. The number of attributes increase the more complicated the controls become. If you do not define each of these attributes, there are assumptions taking place on the writer's end which may not be correct.

Compiling XAML instead of parsing it at runtime will actually eliminate many of the problems experienced by HTML. Primarily because the details (holes) are filled in at compile time instead of the user's computer filling them in at runtime. Therefore, it is not possible for the user to experience a different result then the programmer intended because all attributes have already been defined. Perhaps this is the primary distinction which must be made between XAML and HTML.

I had assumed XAML was parsed at run-time in my previous writings so some of those arguments may not be valid.

-- RyanBates

----

Stop using HTML as the basis of your argument! :) The point of HTML is *not* to visually specify how things should render. CSS is used for that, is there any flaws with CSS (other than incomplete implementations) or PDF?

XAML *can* be parsed at run-time, but as I understand it, it is for speed-reasons that you do it at compile time.

Do you know XSD? When you declare a scheme for an XML document you can write the default value/content for any attribute/tag, so I do not see these holes. There really is no difference between writing:
    
NSWindow* win = [[NSWindow alloc] initWithContentRect:...];
NSImageView* imgView = [[NSImageView alloc] init];
[win setContentView:imgView];

and,
    
<NSWindow>
   <ContentRect>...</ContentRect>
   <ContentView><NSImageView/></ContentView>
</NSWindow>


----

The point is of little importance here. HTML is not used in the way it was intended, and XAML is what HTML was never intended to be. Presentation, logic and data in a very ugly mix. I though we were moving away from the ugly solutions and into an era of clean separation. What M$ is doing is encouraging bad software design. --TheoHultberg/Iconara

----

*I am replying to the part higher up about having a description of the GUI in a markup language is not the same as a Nib.*

I do not really see the difference here. Surely when the Nib is loaded, the same object graph will be in memory as in IB (but the Nib is actually not a true serialization, witnessed by e.g. being able to serialize classes for which IB doesn't have the code), however, the Nib is still just a description for this, and you even have a NibTool to manipulate the Nib, like extracting and changing strings etc.

Likewise, XAML declare how the object graph should be, after the file has been parsed. I really fail to see how declaring in markup, how the object graph should be, and then model the object graph in e.g. IB, and have IB write out a keyed archive, which is a snapshot of this graph...

There is no-one saying that MS won't create a tool just like IB to write out the XAML, they do say that it is likely to be generated by a GUI editor, and they also say, that the markup will instantiate objects -- just like the Nib instructs NSBundle to send initWithCoder: to the classes which names are stored in the Nib...

Okay... that was all quite redundant, but I hope I got the point out...

With regards to constructing GUIs on-the-fly, there are some programs where this is really necessary, like the various rule editors found in Apple applications -- have you tried writing this by hand? it's quite frustrating... not that a markup language would really help, what helps is auto-layout (which is sort of implied with markup, but not mandatory of cause).

----

Wow... that's enough flames for a chilli festival... sounds like fun

Anyone checked out GSMarkup via the Rennaisance project as a new GUI alternative for GNUStep?  You should... there is no difference between how XAML and Nibs work when it comes down to it, except that plain text (GSMarkup or XAML) can be edited easily in any editor, generated (if you REALLY wanted to... blech), stored in CVS nicely, parsed with perl for (perl -pe 's/MyDocument/TheRealSlimShady/'), and play nice with the thousands of other unixy tools on your mac, which the binary format doesn't.  Try reading EricRaymond's other book (not the CathedralAndBazaar) the ArtOfUnixProgramming.  You'll find many good reasons for plain text formats--more than I can mention of the top of my head.  The primary difference is that Nibs have only one editor, and while it's REALLY nice, the binary format doesn't play nicely with anything else, whereas XAML has many options for creating, editing, manipulating, storing etc. but at the moment lacks a REALLY nice editor.  hmmm... what if IB outputed GSMarkup or XAML instead of a binary format...  (really, before coming back to flame skim some of the ArtOfUnixProgramming... it should be available for free reading online, it was when I read it.)

*it's at [http://www.faqs.org/docs/artu/]*

----

So compiling the XAML to bytecode is analogous to using 'Save as PDF' on a web page, is it? I.e. it removes the possibility of vendor/environment differences in interpretation.

Only, I've been using IoLanguage lately, and am starting to rewrite Lodestone in it, which basically amounts to writing a declarative system for e.g. GL graphics. But it's not exactly declarative, because this is Io, and like with Lisp, data and code aren't all that different. So we have abstractions (functions) which return e.g. an XYZ vector, or which return an RGBA colour, and the combination of a few of these is (voila) a shaded surface. Except it's not, it's actually just a bunch of vectors-- some of them being geometry, some being colours, and all of this is driven by a drawing object which asks the abstractions to evaluate themselves to their result vectors, and then plots said vectors.

So it's interesting to me that one can have what is apparently declarative code (*such and such is such and such*, rather than *you, do this*) which is still very stable. This is no doubt because I have the benefit of not having vendor/environment differences because I'm not writing a spec., I'm writing an implementation. And it seems to me that if Apple was going to go the declarative route, they would probably do the same sort of thing: write a framework implementing it, there's your spec. right there. If another implementation didn't live up to Apple's implementation's behaviour, this second one would be considered buggy. And Mac OS X programmers wouldn't have to care because we'd have Apple's.

That would be my guess.

-- RobRix

----

Macromedia is apparently also following the trend: MXML is a declarative language to create user interfaces, it is compiled into SWF-files with a tool called Flex. --TheoHultberg/Iconara

----

My company has created an xml->object tool for java.  The xml translates directly into object instantiations and method calls, as long as methods are named appropriately etc.  The result is xml that has the same structure as java code, but in a form that is more compact and mimics a GUI containment hierarchy.  We parse this on the fly during development and translate it into java code that is then compiled for deployment.  There is absolutely no difference in the resulting application - only the fact that in one case objects were created through reflection and expensive string-matching method lookups, and in the other they were created by compiled code.  The resulting objects in memory are exactly the same.  But writing xml is much faster and easier to think about than writing java.

Now, this was tricky to get right, there are a lot of subtleties, and it only makes me hate java slightly less.  It would probably be no harder, perhaps easier, to do the same for a subset of Objective-C.  It's really not different from a programming language, except that it's XML so clients trust it more and it's less expressive.  This goes back to a basic principle of computer science: any program can be written in any sufficiently expressive language, so the important question is how well does a given language facilitate a given task.

That said, I love IB and wouldn't exchange it for anything I've seen.  The only reason companies like MS and Macromedia do this stuff in text is that every existing graphical editor except IB sucks, and building a good one is hard.  (Hard work, I know how hard it is, it's why we didn't write one for our java thing)  I too would like a text-based nib format (which, I can't emphasize this enough, would not be substantively different from what we're talking about with XAML) but nibtool fills that hole fairly well -- I just wish they'd fix the first bug (see man nibtool).

-- AndrewZeldis

----

I can't see why people actually would think that writing code in XML is preferable to writing it in a traditional language (I mean, why do people seem to like     <logic:iterate name="array" id="item"> loop body </logic:iterate> more than     foreach ( item in array ) { loop body; } ). I can't stand all the "<" and ">" and the rest of the syntax. OK, it's easy to parse into abstract syntax, but that's making it easy for the compiler, not for the programmer! It's not like we need to do that. XSL is not a good functional language syntax-wise, and o:XML sucks for the same reason. It's a step backwards when it comes to the usability of programming languages.

--TheoHultberg/Iconara

----

I don't think the point is to write code in XML, the goal is to declare whatever needs declaring (w/o writing code).

This has some advantages like a) probably less bugs (i.e. declare that the data type for a string gadget is �E-Mail� rather than program a validation function), b) easier to �transform�, e.g. you could (hypothetically) generate a Web-form from your GUI description, but not from the code that creates the GUI, c) most likely easier to make tools that generate the GUI, etc. etc.

--AllanOdgaard

----

XML is good for data, but not really for code (I also tried it once). My take on InterfaceBuilder versus code-interfaces: many people *like* WYSIWYG HTML editors (lots of acronyms), so it's not like everyone prefers declarative writing. SUGGESTION: nibs should be stored in XML format, or (like OpenGL, I think), in actual Objective-C and/or Java code that can generate the interface.

--JediKnil

----

Nibs *can be* stored in an XML format (save it in the "text" format in IB), but I don't think it's really human-readable or editable when doing so, despite being XML.

----

I didn't see this anywhere else on this page, so, Renaissance: http://www.gnustep.it/Renaissance/

I got to this by way of bbum's weblog: http://www.pycs.net/bbum/2004/12/2/#200412021

*Renaissance: Amazing, I didn't know you could say "intuitive" so many times in one paragraph --Theo*

