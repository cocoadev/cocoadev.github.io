---
layout: page
title: SearchKit
---

SearchKit Documentation

*Conceptual � http://developer.apple.com/documentation/UserExperience/Conceptual/SearchKitConcepts/index.html
*Reference � http://developer.apple.com/documentation/UserExperience/Reference/SearchKit/index.html


Anyone have any insight regarding how to implement searching using the SearchKit API?  In conjunction with NSSearchField?

-- SashaGelbart

----

I've re-surfaced this discussion in the hope that someone may have a simple tutorial on using SearchKit.

Personally my needs are very simple, and to tell the truth I could just use an NSSearchField and 'fudge' the searching just by writing some very simple code myself, but I'd like to give SearchKit a go. Problem is it seems complex to use, and the Apple docs aren't much help, more of a reference. For those of us who want to make use of the SearchKit API but are having difficulty finding a more easily digestable place to start, does anyone have any recommendations?

Thanks!

----

*Note: the below description is for 10.3's SearchKit.  The searching method described is deprecated in 10.4; faster functions are now available.*

Here's everything I know about SearchKit. It's not very much, but it might help you (or someone else) get started.

There are two processes involved in searching. First you index the documents, then you search the index.

**Indexing**

There are three types of indexes: inverted index, vector index, and inverted vector index. I don't fully understand what each of these do, but inverted index seems to be the best for basic queries. Inverted vector index is best used when you are finding documents similar to another document. An index can be stored in memory or in a file.

To create an index you must supply a document. You can tell SearchKit to index a file (Text, RTF, PDF, Word, etc.) or CFString. This means SearchKit can index pretty much anything you want. Once SearchKit has an index, it no longer needs the original document. A reference to the document is stored in the index (such as the file path). This reference is returned when you process the searched results.


**Searching**

Searching is a three step process:


1. Create the Search Group

A search group is a set of multiple indexes. For example, Mail stores each Mailbox as a separate index. You can search all mailboxes by making a search group of all of the indexes. Often you will only need one index in a search group.


2. Supply search query to SearchKit

SearchKit supports several different kinds of queries: (Mail seems to combine several of these queries?)

* Ranked - this is what Apple Help uses and is a natural language query. It will remove common words allowing the user to submit complete questions. I believe this is the only query type which returns the relevance of each document that was searched (?).
* Prefix - This will search the beginning of words which allows for instant feedback. Finder and Preview use this.
* Boolean - Supports and/or searching through the query string (maybe through & and | characters?).
* Document - Instead of providing a string, you provide a document and it will find similar documents. It is best to use an Inverted Vector Index for this.
* Plus/Minus - Sorry, don't know much about this one. I'm assuming it allows you to do "not" searches?



3. Process Results

There are several things SearchKit provides in the results:

* Document Reference - The reference you gave for the document when indexing (e.g. the ID to a message in Mail).
* Index Reference - The index the document came from (e.g. a mailbox in Mail)
* Relevance - how much the document matches the query.


I hope that helps.

-- RyanBates

----

Apple added some conceptual information. Link at top.

Sample code: http://deallus.com/SearchKitExample1.zip

----

Wouldn't Spotlight render SearchKit superfluous?   *Isn't that alliteravely alluring? ;)* **Whatever would we want with last week's ware?**

----

Spotlight builds on SearchKit. Also, Spotlight only works on files.

----

I'm working on a cocoa wrapper for SearchKit. you can get it here http://github.com/simonmenke/coresearch/tree/master 

----

To my knowledge the above link is no longer available, and the CoreSearch wrapper no longer appears in searches. On June 9, 2011 I released SPSearchStore, a cocoa wrapper for SearchKit that exposes the expected indexing and querying capabilities as well as the complete two-way term and document mappings contained in a SearchKit index. The code requires Mac OS 10.6+ and is available under a BSD license. It may be found at: http://phildow.net/source-code/#SPSearchStore ~Phil

