---
layout: page
title: HTMLParsing
---

Hi all,

I am now an experienced Cocoa programmer, but one thing has been bugging me for a quite a while now. I moved from VB and VC++, and there was a function which allowed me to call in a file and read through line by line and do something with these lines (usually save into an array). I was wondering, is there any equivalent for Cocoa? I really need it for parsing an HTML file.

If anyone has any better ideas for parsing the file, feel free to enlighten me as I am all ears.

Thanks for you time.

----

I've been programming in Cocoa for about two years (C for about 5), but I'm not much of an HTML expert. Objective C isn't the easiest language to parse text. "sed" and "awk" are great command line text tools if you know them. If you know Perl, I've heard that people have built Cocoa objects that wrap it. 

There are a bunch of really knowledgable Cocoa programmers that frequent this site (I'm not one of them), so you have a good chance of getting some useful info. 

If you have to parse text with Objective C here are some useful things I've come across.


* NSString *html=[NSString stringWithContentsOfFile:file];
* NSArray *lines=[html componentsSeparatedByString:@"\n"];
* always use NSEnumerators when searching every line. objEnum=[lines objectEnumerator]]; while (obj=[objEnum nextObject) {}; 
* RegularExpressions - http://www.lorax.com/FreeStuff/MOKit.html
* look closely at the documentation for NSString and NSMutableString. These two classes have about every low level text edit function you would need to build your own object to parse HTML. 


--zootbobbalu

----

Thanks for that tip. After alot of mucking around, it now works like a charm (I had to add code). Again, thanks. :-)

----

Could anyone post some source code? I'm trying to figure out how to work around the HTML tags. Thanks.

----

Take a look at the boost spirit libararies.  I use them for my parsing and they work great.  They're C++ libararies and they allow you to inline EBNF grammar descriptions straight into your C++ code and then assembles the parser at compile time (dynamic options also available).  You can just use Objective-C++ and have these libararies do the grunt work of parsing.  They work just as well as lex and yacc just save you the time of compiling seperate programs and so forth.  Here's a link: http://www.boost.org/libs/spirit/index.html

Code might look something like this:

void handletag(const char * first, const char * last)
{
std::string str(first,last);
std::cout << "This is what's inside the tag: " << str;
}

rule<> html_tag= ch_p('<') << (*anychar_p)[&handletag] << ch_p('>');

parse("<tag somestuff>",html_tag,space_p);

- FranciscoTolmasky

----

I read the HTML I wanted to parse into an NSString, then used AGRegex to split the input into chunks - tag-oriented rather than line-oriented. (Don't assume an HTML file is neatly split into lines.) The RE I used was @"[<>] *[<>]*". This gave me an NSArray in which element was one of (a) an opening tag, possibly with attributes; (b) element content (i.e. CDATA); or (c) a closing tag. 

RickInnis

----

I'm surprised nobody has mentioned NSScanner yet. It lets you walk over the characters in an array until you hit a particular string, or a character out of an NSCharacterSet, and lots more.

If all you need to do is display HTML, you may want to check out NSAttributedString (which has a method to load HTML-escaped and -styled text) or Apple's brand-spankin' new WebKit. The latter provides a "browser view" which you give a remote or local URL and it loads it, including tables, plugins etc. -- It's the same engine Safari uses, and it actually requires Safari 1.0 to be installed.

UliKusterer

----

with regard to NSScanner, mentioned above: NSScanner is *much* slower with character sets than with strings. see my NSScannerTimeTrialSourceCode. *--boredzo*

----

I'm not convinced that regular expressions are really robust enough to handle HTML in all its mess... Check out the el-kabong HTML parser (ekhtml.sourceforge.net) and the usage in the cocoa app Blapp - blapp.sourceforge.net - the code in question is here: http://cvs.sourceforge.net/viewcvs.py/blapp/blapp/LinkWindowController.m?rev=1.23&view=auto

----

Having written both a Perl Compatible Regular Expression Library and a HTML 4.01 compliant web-browser I can only second the latter opinion :)

HTML is recursive in structure, regular expressions are not -- there exists extensions which allow them to perform recursive matching, but this exclude the possibility of captures, which makes them good only for verification, not "parsing".

Using NSScanner or similar does not strike me as a benefit either -- if we were to parse valid XHTML we could do with something like this:
    
template <typename _Iter>
struct TagInfo
{
   bool has_children;
   bool is_close_tag;
   _Iter begin_of_tag;
   _Iter end_of_tag;
   std::string tag_name;
};

template <typename _Iter>
TagInfo<_Iter> get_tag_info (_Iter first, _Iter last)
{
   TagInfo<_Iter> res;
   res.begin_of_tag = first;
   ++first; // skip '<';
   if(res.is_close_tag = *first == '/')
      ++first;

   _Iter begin_of_name = first;
   static const char ws[] = { ' ', '\n', '\r', '>' };
   first = std::find_first_of(first, last, &ws[0], &ws[sizeofA(ws)]);
   res.tag_name = std::string(begin_of_name, first);

   // parse arguments
   ...

   static const char endChar[] = { '/', '>' };
   first = std::find_first_of(first, last, &endChar[0], &endChar[sizeofA(endChar)]);
   res.has_children = !(first != last && *first == '/');

   first = std::find(first, last, '>');
   if(first != last)
      ++first;
   res.end_of_tag = first;
   return res;
}

template <typename _Iter>
_Iter parse_xhtml_buffer (_Iter first, _Iter last, Tag& parent)
{
   while(first != last)
   {
      _Iter begin_of_text = first;
      first = std::find(first, last, '<');

      if(begin_of_text != first)
         parent.add_text_node(begin_of_text, first);

      TagInfo<_Iter> const& info = get_tag_info(first, last);
      first = info.end_of_tag;

      if(info.is_close_tag)
         break;

      Tag& newTag = parent.add_tag_with_info(info);
      if(info.has_children)
         first = parse_xhtml_buffer(first, last, newTag);
   }
   return first;
}

I left out the actual Tag structure, it could be:
    
struct Tag
{
   std::vector<Tag> children;
   std::string tag_name;

   Tag (std::string name) : tag_name(name) { }

   Tag& add_tag_with_info (TagInfo const& info)
   {
      children.push_back(Tag(info.tag_name));
      return children.back();
   }

   template <typename _Iter>
   void add_text_node (_Iter first, _Iter last)
   {
      // well, we should have a tag base class and a text subclass... doh!
      children.push_back(Tag(std::string(first, last)));
   }
};

The above can be modified to also parse properly nested HTML -- the changes are that rather than look after a trailing / in the tag, then a table lookup should be used to check, if the tag can have children.

Furthermore, rather than blindly return when seeing a close tag, it should check if the close tag is the same as the current parent tag, if not, it should still return, but set a state indicating a pending close tag, so that the parent tag does the same check a.s.o., since e.g. "<b><i>bla</b>" is actually valid HTML (here </b> closes two tags).

A final change is to keep open counts for all tags, because some tags will close others, e.g. <p> will close any previously open <p> tags. Unfortunately these open counts needs to be pushed and popped on some tags (like entering a <table>) -- unfortunately the de facto rules for these things are very weird -- I think it stems from the first browsers not using recursion to parse HTML, and treating many tags as toggles rather than openers/closers.

*That explains why OpenGL code often reminds me of a markup language...*

----

I honestly don't see why anyone would want to go through all that work when Spirit has provided a very EFFICIENT and LIGHTWEIGHT solution in their libraries (described above).  I did basically the same thing in 4 lines as the above.  Spirit custom tailors a parser at compile time, and even provides options for error handling.  The best part is, it's almost straight EBNF, so if you can find a good EBNF description of HTML online, you can translate it to the inline C++ Spirit stuff and not even have to worry about writing it correctly.

- FranciscoTolmasky

*Because HTML can not be described using EBNF.*

Did you not see the example above?  Extend that a little and you're set.

*But the example above only parses strict XHTML, HTML has a dozen rules which cannot be expressed in EBNF, some are also mentioned above.*

I honestly don't see anything that wouldn't be doable with Spirit, maybe not with EBNF(?), but certainly with the Spirit libraries, care to give an example?

----
Here is one:
    
<ul>
 <li>First Item
 <li>Second Item
 <li><table border><tr>
  <td><ul>
   <li>First item in table
   <li>Second item in table
  </ul></td></tr></table>
</ul>

In this example <li> must close the currently open list item, except when it is within the table. I do not know how to express such things (easily) in EBNF.

Another example which most browsers get wrong (most likely for compatibility with broken pages):
    
<p>A paragraph above
<dl>
 <dt><b>The term in bold
 <dd><em>The definition with emphasis
</dl>
<p>A paragraph below

Here bold should apply only to the definition term (<dt>) and emphasis to the definiation data (<dd>) -- browsers generally tend to show the rest of the text in bold/emphasis (but if a stylesheet is added to the page, I think most browsers behave as they should), but this would in fact be even harder to reproduce with EBNF.

hmm... come to think of it, how on earth are you going to write a generic parser (i.e. in EBNF) which can tell wether or not a tag spans children? Take these tree examples:
    
<p>Some text<br>Other text</p>
<p>Some text<p>Other text</p>
<p>Some text<b>Other text</p>


The 3 parse trees are different:
    
<p>
|- Some text
|- <br>
\- Other text

<p>
\- Some text
<p>
\- Other text

<p>
|- Some text
\- <b>
   \- Other text


But if you feel so convinced that Spirit is the way to go, then why not try to write the actual parser? maybe then you won't find it so hard to understand why people bother writing custom code...

----

I think you confuse language grammars with actual implementations.  EBNF just describes the language grammar, meaning it would give an error if you typed "</tr>" without having first typed "<tr>".  The problems you described were solely in the actual conversion process, which is handled by semantic actions attached to grammars, not the grammar itself.  What gets attached where is not handled by a grammar.  For example, if I understood correctly, in the first example I don't think the problem is the table but the fact that the li is within an additional ul.  Seems like just a stack issue to me.  something like:

void handletag(const char * first, const char * last)
{
listItem *someItem;
if(someItem= listStack.top()) someItem->close();
}

rule<> li_tag= str_p("<li>")[&handlelitag];

something to that effect.

Notice the grammar is just a string parser for "li".  The code behind it does the actual figuring of which li should be closed.  I could write an entire grammar and not have it do anything useful except tell you if the code is correct or incorrect.  Or maybe I misunderstood what you were trying to say.  

Regardless, the reason I "don't write the actual parser", which I think was a somewhat childish proposition, is that I do have my own projects.  Maybe you go around writing other people's code, but frankly I don't have time to write an entire HTML parser from Spirit just because someone needs it, especially when it has nothing to do with what I am working with.  I have however used Spirit before and that is only why I am suggesting it, this isn't some sort of competition.  I used it to write a, somewhat simple compared to HTML, markup language and had good (and fast) results.  That is all.  It is the first time I have ever been attacked for suggesting someone not reinvent the wheel... isn't that almost rule number 1 of computer science?

- FranciscoTolmasky

----

oh, come on -- I spent time answering the original poster, I spent time trying to explain you (after your rather arrogant statement) why Spirit/EBNF is not the key to HTML parsing, and my suggestion for you to write the parser was that if you succeed, your contribution to this wiki would be of tremendous value, and should you fail, then you would probably get some insight that you currently do not hold, which I'd say is win/win, and not an attempt of a childish pissing contest or whatever you thought.

With regard to your belief that I do confuse grammar and implementation, no I do not -- but I did not consider your approach about using Spirit merely to tokenize the input and use semantic actions to assign structure to the input (which is normally the job of the parser). I would however deem this approach unfeasible, as you would end up with semantic actions for all 101 (IIRC) tags, and all of these need to figure out context somehow, which is recursive, and thus a recursive parser with table support would be ideal for this.

Chomsky define a hierarchy of languages. At the top we have regular languages, parsed e.g. with a DFA/NFA, next the context-free languages, which Spirit will parse using a recursive descent parser (AFAIK), but next again there are the context-sensitive languages, where I believe HTML belong, and this cannot be parsed (i.e. structure be assigned to the input) using BNF -- so we need to write custom code. But we might make this task easier by defining a regular language which just define tags, and then use e.g. a DFA to parse this regular language, and then do our context-sensitive parsing on the transformed input, which I think is what you really wanted to suggest -- but then a) there are better tools than Spirit and b) I do not think this would really be a benefit, because HTML consists of 90% data and 10% tokens, and there are very few tokens (basically just tags), so the benefits of removing this from the actual parser is questionable, because it comes with drawbacks, like the lack of progressive parsing and introducing a layer of abstraction which probably will be as big as the actual tag-parsing functions.

----

Referring to the previous posting about using perl (or just regular expressions) within Cocoa, yes, there are apparently several ways to do it.  I haven't tried any of them yet, but here's what I've found out:


*You can use MOKit from Sourceforge.net.
*You can use Apple's PerlObjCBridge. 
*You can use CamelBones.
*You can use the C based regex libraries.  (try man regex to learn more).
*You can call a utility such as grep, sed, awk, etc. from the command line using NSTask.


I assume that the preferred way is to use Apple's bridge since that is likely to be developed more in the future.  I always cringe away from using third party solutions because then you're up a creek if they stop developing them.

----

Has anybody looked at libxml's HTML parsing module? There's a version included with OS X's bsd libs, and a new (opensource) version available that I had no trouble building on Panther. For getting at the structure and content of an HTML page, it seems pretty powerful.
--Paul Collins

----

The skyrix-xml frameworks contain a SAX driver bundle based on libxml2 for parsing HTML (and XML):

  http://www.opengroupware.org/en/devs/sope/skyrix_xml/

using either SAX or DOM. The libxml2 HTML parser works pretty well in practice.

----

Didn't see it mentioned, but on Panther you can use NSXMLParser. I've not done this with HTML files, nor have I attempted to use CXLXMLModel on parsed HTML files, but if the latter doesn't work now we can always fix it.

-- RobRix

----
You may also want to consider using htmltidy library in conjunction with and xml parser of which many abound.  There is a library but I have not investigated the API. Sure would be nice to have a ObjC wrapper for tidy.

- jason

----

Seems that the question of the class of language to which HTML belongs was never agreed upon. Guess that depends on which HTML version. Fortunately, other people have done some work:

http://www.oasis-open.org/cover/sgmlprodAgnew.html � The author claims to have hand-written an LL(4) grammar for SGML in EBNF.
http://www.xml.com/pub/a/98/10/guide1.html � claims that SGML cannot be described in EBNF due to technical issues; XML can be (no grammar provided).
http://www.oasis-open.org/cover/topics.html � Provides information about SGML grammar, including a set of productions which seem to be in EBNF. Good resource.
http://www.oasis-open.org/cover/english-cfg.html � Explanation (sourced in link above) of why full SGML parsing requires Turing machine (PDA/CFL no good)
http://www.sniffer.net/bookshelf_do_sniffer/web/html/appa_02.html � HTML 2.0 grammar

A search for "html grammar ebnf" on Google Groups produced no interesting results (a couple people requesting one).

----

An option I did not see mentioned is the use of Perl's LWP.  I have an app that uses it successfully to fetch and parse pages, in order to find other links and image files (the app is a web spider of sorts).  To make it all work, I downloaded and built the LWP elements, packaged them inside my app bundle, and wrote a perl script to perform the fetching and parsing.  I then launched the script via NSTask in my app.  The script output (links separated by delimiters) is then piped to my app and used as it becomes available.

Example app at http://www.positivespinmedia.com/shareware/NetScrape/index.html

I can email code samples by request (address at link above), or simply dig into the app's bundle to look at the LWP bundling and the perl script.  Google "perl LWP" for more info about LWP.  You might also enjoy the book "Spidering Hacks" - http://www.amazon.com/exec/obidos/tg/detail/-/0596005776/002-0541816-3295264?v=glance

HTH - JohnPannell

----

In Mac OS X 10.4 and later, you can use NSXMLDocument to parse HTML (and XHTML of course) by doing something along these lines:

    
NSData *data = [NSData dataWithContentsOfFile:@"/Users/blah/test.html"];
NSXMLDocument *document = [[[NSXMLDocument alloc] initWithData:data options:NSXMLDocumentTidyHTML error:NULL] autorelease];


You can then treat HTML like XML. Note that this is HTML, so it'll even work if you have <br> instead of <br/> in your code.

