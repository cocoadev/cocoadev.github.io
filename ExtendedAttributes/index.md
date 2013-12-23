---
layout: page
title: ExtendedAttributes
---

Extended attributes extend the basic attributes associated with files and directories in the file system. They are stored as name:data pairs associated with file system objects (files, directories, symlinks, etc).
----
Can we write a spotlight importer for extended attributes and make it searchable using spotlight? if so can one tell how to go about it?

----
Check out the opensource project http://www.fluffy.co.uk/spotmeta/ which does just this... However, one thing to watch is that ONLY modifying extended attributes does not cause spotlight to trigger.
----
Great! never thought i would get reply soo soon. any way i will look into that. i am sure if you just add extended attributes it wont be indexed to show in spotlight. but i wanted to know wether we can write importer for that. will soon look into your pointer
----
I wrote a importer which gets the extended attribute and adds it. Now spotlight can see my keyword. But whati feel is this violates the the reason why we go for extended attributes. because importer will add the keywords metadata [keywords will be listed if you execute a command 'mdls']. we chose extended attributes becoz we donot want to add it to metadata.
----
I am writing a importer for images. i have given the LSItemContentTpes as public.jpeg. when i debug my importer using mdimport -d1 path it shows my importer. [it uses my importer] but it wont display apple shipped image.importer. Why it is like that? even though it doesnt load image.importer spotlight works perfectly. How can i make both importer to load?

----
If there are multiple plug-ins for a particular file type (e.g. a generic image metadata importer and a JPEG metadata importer), the most specific plug-in wins. In other words, only one plug-in gets a shot at each file. I only hope Leopard fixes the hack known as spotlight....
----
What is exported/imported UTI? I have read the documentation soo many times. still i could not understand it [from here http://developer.apple.com/documentation/Carbon/Conceptual/understanding_utis/understand_utis_conc/chapter_2_section_4.html#//apple_ref/doc/uid/TP40001319-CH202-BCGFBBIA ]. can any one exaplain here in nice way?
----
If you provide the definition for a type, you export its UTI.  (Example: Word would export something like com.microsoft.WordDocument)  If you can use those files in some way, but they're not "yours", you import them.  (TextEdit, if it supported importing Word documents, would _import_ com.microsoft.WordDocument).
----
Yes, and TextEdit does open Word documents.  Why don't you look at the source code to see how ?
----
Is there any way to edit kMDItemStarRating,kMDItemKeywords of images? I know there are methods to read those fields but could not find how to edit.

----
The answer is yes, though I believe the API to do so is non-public.  Note, please note, if you edit/add metadata then it will disappear as soon as the file is changed/touched (i.e. the normal importers run).   So reliable/dependable tagging this is certainly not!  Thus this explains why people (above) have made attempts to store their meta-data in the extended attributes instead, and then try to something 'clever' with the importers.  

----

I've been thinking about tagging a little lately and I'm thinking of just adding my tags to the Finder comments of the file. 
Seems like a safer way to go. Maybe.
----
Can you please reveal the api set to edit the attributes? I wonder what 'clever' above people can do?? becoz they canot write one more importer for any type!  If you 'tag' in finder comments it is not reliable, user might chage it explicitly. One more thing, finder comments are also stored in one of such attribute mentioned above. so why is the finder comments are not removed? I found one more attribute 'kMDItemComment'. I hope this would be better for tagging if we can edit this attribute (better than finder comments atleast). 
----
As a follow-up and a reply to some of the above posters, I've been storing my data in both the "Finder Comment" of the file and in an extended attribute.  However, I can never shake the feeling that my whole system is a big hackjob waiting to collapse on me.  Long story short, I need the capability to store arbitrary information about any arbitrary file, and I need any/all of the information to be searchable via Spotlight.  Basically... I put the data into the Finder comment so Spotlight sees it and indexes it; I put the data in the xattr so I can pull it back easily without too much worry that I've lost any information due to tampering with the Finder comment.

I've also had huge trouble forcing Spotlight to re-index files.  Even if I went the route of writing an generic importer that adds my xattr to Spotlight, I don't know how I'd go about guaranteeing that Spotlight would even re-index that file when the xattr was modified.  Running `mdimport -f` will often have no effect whatsoever; the command will return without error but it appears to harbor some internal notion about what files actually deserve to be re-indexed and disregard the "force" flag from time to time.  My next attempt at 'encouraging' an re-index was to use both `touch` and a Cocoa-ized touch via changeFileAttributes:atPath: with N<nowiki/>SFileModificationDate.  Neither worked.  For now, updating the Finder comment seems to get the job done... even if it's a dirty, dirty hack. :-/

Any insight is very welcome.  I'd dump the system I've got in a heartbeat if I could come up with anything better (short of reinventing the wheel).

----
Since they are named Finder comments it would seem logical that asking Finder to update them (via Applescript I assume) is the most correct and reliable way as opposed to the adhoc touch/mdimport.
----
The reason I tried the touch/mdimport methods was that while the Finder flawlessly updates the comments (they appear in the Get Info window right away), Spotlight does not always re-import them.  A small amount of latency would be expected, I know, but on occasion the Spotlight database is *never* updated for the Finder Comments of the file until the file is physically altered somewhere down the road (ie, the image is edited, the text file appended, etc).

So far none of my users have reported the issue... but I know it's there lurking and I haven't been able to find a 100% solution.
----
Does any one knows about how SpotMeta works? I tried analyzing the code. As far as i understand it works like this.      For all the types Initally SpotMeta importer is loaded, then this Importer loads the apple shipped importer or other importer. But i could not understand How the SpotMeta is made to load always?

