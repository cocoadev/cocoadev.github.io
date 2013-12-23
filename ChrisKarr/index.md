---
layout: page
title: ChrisKarr
---

I'm the developer of BooksForMacOSX [http://books.aetherial.net/]. I'm interested in creating and growing a community of Cocoa open source programmers who are interested in developing compatible applications (where it makes sense) and are interested in sharing experience and tips with each other.

In my real life, I'm a software developer for Northwestern University [http://www.at.northwestern.edu] and I manage the technical side of the Oyez project [http://www.oyez.org]. I also keep a personal weblog at [http://www.aetherial.net/].

Current Cocoa Questions:

Brainstorming on the Separation of Content and Functionality (Posted from the BooksForMacOSX weblog - http://books.aetherial.net)

As I've stated many times in previous posts, for myself, the primary benefit that I continue to receive from Books is that it is an intellectual playground where I can implement new ideas and features and have users give me feedback on them.

That said, here's something that's been rolling around in my head lately. In the current state of computing, there is a wall between what people consider applications and data. The application consists of executable code that is fed the data (which is non-executable) and does something useful. In my Books work and during my real job, I've been thinking of whether this distinction must exist.

A nonsense example of my thoughts: Wouldn't it be great if when you used program FooX to create some sort of data about your foo collections that you could send your FooX data files to your grandmother to show her what a good job you've done building your foo collection. She receives the file via e-mail and rather than have to go out and search for the FooX appplication (or another that that can open these data files), she simply double-clicks the file you sent and she can view the data you have sent, despite the fact that she's never worked with such a file in her computing life. The FooX data file that you have exported includes a small read-only viewer that allows Grannie to see the contents of the file without having to resort to searching for it on Google, VersionTracker, or any other type of site. In this case, the FooX file you sent Grannie is completely self-contained and useful by itself, without any additional work.

Now, let's translate this example so that it uses Books. You build a nice collection of books and find a subset that you'd like to let Grannie know about, thinking that she would be interested in them herself. Since you don't know whether Grannie has ever heard of Books or is able to even install it. At the moment, you would build an export that you know she can probably read, such as RTF, plain text, or HTML. However, you'd rather make things easier on Grannie, and allow her to search for books, sort them, and launch them on Amazon - as easily as it can be done. Given MacOS X's architecture, this is completely do-able - you'd export a .books file (which is actually a bundle) that includes the data you are exporting, but it also includes a small viewer for handling the data. If people have a full-blown copy of Books on their machine, double-clicking the file opens the file in Books, with all the functionality enabled. If Books is not present on the system, the file still opens, just with the smaller embedded viewer that includes a subset of the functionality.

Now, there are some good reasons for not including executable code with data, as we've seen with the countless Windows viruses that propagate via Outlook. However, if we were to limit the functionality of the embedded code to be read-only, the potential for damage shrinks substantially. Another concern would be that an attacker could post a normal-looking Books file with normal data, but with a malicious payload for the viewer instead of the simple normal viewer. I'm sure that there are other issues, but I'm simply brainstorming at the moment.

So some questions for you users:

1. Would you like the functionality mentioned above in Books? That is, if you could export your .books files in forms that people without Books (or even without a Mac) could read without searching for or installing additional software?

2. Is this a bad idea that opens up a Pandora's box? Is the gain in convenience and functionality worth the risks it introduces? One risk would be that someone could distribute their own version of Books that includes a malicious payload in .books files instead of innocent viewers. On the other hand, what's to stop people from posting modified copies of Books (the source is open) that does something similar from within the main application?

3. It would be relatively straightforward to write export code that made .books files executable on the Mac without Books by using the tools already available in the OS. However is this feature pretty pointless unless you can send .books files to Windows and Unix users and expect the same behavior? It would be straightforward to implement a cross-platform viewer using Java, but is this just shifting the burden on the users to check that they have installed Java instead of Books?

4. Perhaps this should be an export separate from .books files, and thus would be yet another way to export data instead of being the default. Would people use an export option that basically exported a program with the book data embedded with the viewer that could be run on machines with Java? (FWIW - this is exactly how the J2ME export option works.) Instead of exporting just HTML or text files, you could export JAR files that could be executed on Java systems and perhaps even integrated with other Java-based software. (i.e. Other Java applications could include the JAR file as a library and make method calls to your exported library - get book list, get a book, get a book's cover image, etc.)

5. Other thoughts?

To me, this is an interesting idea but I'm not quite sold on the ultimate utility of it. I look forward to all the feedback and questions that this idea may bring up.

