---
layout: page
title: ChooseYourOwniPhoneURLScheme
---

iPhone applications can provide services to other applications by providing URL schemes; these schemes can be documented so that other applications can actually make use of these services. This page intends to be a place dedicated to this.

Reply to this page to document your own schemas. Feel free to copy the schemas above you as templates.

Before we start, **two definitions**: below, a **private scheme** is a scheme that *a single application* implements. You can be "sure" that only that application will be launched when using it. A **public scheme** is a scheme that *multiple applications* can implement, and that *you are encouraged to add to your own application*. This can be useful if a service can be provided by multiple apps and the application requesting the service does not want to tie to a single one of them.

----

These and more URL schemes are registered at http://handleOpenURL.com, a website aimed at servicing iPhone developers with a centralized index of used and usable URL schemes. If you have any ideas or would like to make contributions, pleas contact me at mailto:mwolzak@magnatron.nl
Maarten Wolzak (September 10, 2009)

----

**twitterrific:** -- A private URL scheme used by the Twitterrific application http://itunes.apple.com/WebObjects/MZStore.woa/wa/viewSoftware?id=284540316&mt=8 by the IconFactory.

**Action: Preparing a message**: This will prepare a message, which the user will be able to post to Twitter.

To use, launch a URL of the form      twitterrific:///post?<query> , where     <query> is a query string with the following key-value pairs:


* **post**: *a string* -- The message that should be posted to Twitter. It should adhere to Twitter's character limits on messages. Twitterrific will launch with the message field visible and prefilled with this message.


----

**com-innerfence-ccterminal:** -- A private URL scheme used by the Credit Card Terminal application http://tinyurl.com/5q935d by Innerfence.

The original two-way URL-based IPC implementor.

Details regarding this URL scheme are encapsulated in a set of open source classes that you are encouraged to use. See http://blog.innerfence.com/2009/01/05/2-way-app-integration-on-the-iphone-how-it-works/ for details.

----

**x-infinitelabs-diceshaker:** -- A private URL scheme used by the Diceshaker application http://infinite-labs.net/diceshaker/ by EmanueleVulcano.

**Action: Posting a message**: This action is only available with Diceshaker 1.0.4 or later. When used, it will allow the user to roll dice in the Diceshaker application and return the result to the calling application. Diceshaker displays a "Send" and a "Cancel" message; both return to the calling application with an appropriate result.

To use, launch a URL of the form      x-infinitelabs-diceshaker:roll?<query> , where     <query> is a query string with the following key-value pairs:


* **sender**: *a string*, mandatory -- A short title identifying the application. Will be displayed to the user as part of a prompt.
* **returnURL**: *a return URL*, mandatory -- A correctly escaped string which, when decoded, is an URL that will be called with the result of the operation. See below.


When the user has finished rolling the dice and chooses a return option, the return URL provided above will be opened. The URL will be unmodified except that one or more key-value pairs will be added to the end of its query string part. If the URL appears to not have a query string part, then '?', followed by the result key-value pairs, will be appended at the end of it before opening it.

The items that will be appended are:


* **canceled**: *a string*, optional -- If present and if its value is equal to the string 'YES', then the user canceled the operation.
* **numberOfDice**: *an integer*, optional -- The number of dice that have been rolled. For example, if the user rolled three six-sided dice, this key's value is '3'. Present only if the user didn't cancel.
* **numberOfFacesPerDie**: *an integer*, optional -- The number of faces that each rolled die had. For example, if the user rolled three six-sided dice, this key's value is '6'. Present only if the user didn't cancel.
* **roll**: *an integer*, optional -- The sum of all rolled dice. For example, if the user rolled three six-sided dice, and the dice came up 3, 2 and 5, this key's value is '10'. Present only if the user didn't cancel. (Note that the value of individual dice is not returned to the caller, only the sum of the roll.)


http://www.screencast.com/users/millenomi/folders/Default/media/31390729-aabe-41da-a9e3-b48862095f7a shows an example of use of this action. Source code can be found at http://github.com/millenomi/iphone-ipc-sample-diceshaker/.

----

**x-sadun-services:** -- A private URL scheme used by the Ad Hoc Helper application by Erica Sadun. Details found here: http://wiki.github.com/ars/copy-paste


* x-sadun-services:command?param1=p1&param2=p2&param3=p3&param4=p4&param5=p5
* command: paste | copy | clear | type
* params: scheme | data | clipboard | password | expire | type


----

**thetapoker:** -- A private URL scheme used by the THETA Poker application by Robert Jen.

Currently, thetapoker://help launches THETA Poker on its Help screen, while any other invocation simply launches the application. The syntax will be expanded in the future to allow viewing specific hands and more.

----

**tweetie:** -- A private URL scheme used by Tweetie: http://www.atebits.com/software/tweetie/ by atebits.

**Action: Preparing a short URL to post**: This will prepare a message pre-filled with a given URL automatically shortened with bit.ly

Launch a URL of the form      tweetie:URL 

**Action: Preparing a message**: This will prepare a message, which the user will be able to post to Twitter.

Launch a URL of the form      tweetie:///post?message=URL_ENCODED_MESSAGE 

----

**instaviz:** -- A private URL scheme used in the diagram sketcher, Instaviz: http://instaviz.com by Pixelglow Software.      instaviz:  URL's always use an "inner" URL that expresses the graph you want to display, for now Instaviz understands      data: ,      http:  and      https:  inner URL's.

**Action: Display a graph embedded in the URL**: You can embed a graph in the URL using an inner      data:  URL and the DOT language. See http://en.wikipedia.org/wiki/Data_URI_scheme.

Launch a URL of the form      instaviz:data:,digraph G { hello -> world; } . Note that you will have to %-encode the usual punctuation for this to work from an app.

Launch a URL of the form      instaviz:data:;base64,ZGlncmFwaCBHIHsgaGVsbG8gLT4gd29ybGQ7IH0K . You can also base64-encode the graph so that the misaligned %-encoding bug will not prevent the URL from opening. If your graph has lots of punctuation, base64 encoding may actually be shorter.

**Action: Display a graph referenced by the URL**: You can reference a graph for Instaviz to download using an inner      http:  or      https:  URL.

Launch a URL of the form      instaviz:http://www.graphviz.org/Gallery/directed/fsm.gv.txt 

See http://www.graphviz.org/doc/info/lang.html for how to compose graphs in the DOT language.

----

**xkcd:** -- A private URL scheme used by xkcd app: http://bit.ly/rZtDq by Treeline Labs; works as of version 2.0.

**Action: Open an xkcd comic**: This will open the xkcd app to the specified comic, download it, and open it.

Launch a URL of the form      xkcd://537 

----

**fb:** -- Facebook Link

ie: a href="fb://profile/1415672410"


----

**stanza:** -- Launch an ePub/PDF/CBR/CBZ/DjVu book HTTP URL in Stanza

**stanzas:** -- Launch an ePub/PDF/CBR/CBZ/DjVu book HTTPS URL in Stanza

**stanzacatalog:** -- Open Stanza and add a new Stanza catalog entry with the specified HTTP URL

----

**kml:**  -- Launch a KML file HTTP URL in Google Earth (public, but probably buggy. David Phillip Oster, August, 2010.)

Reserved for the future by Google Earth: 

**kmz:**  -- Launch a KMZ file HTTP URL in Google Earth

**geo:**  -- Fly Google Earth to a specific latitude and longitude

