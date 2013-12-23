---
layout: page
title: AliasManager
---

The Alias Manager creates and resolves Aliases and AliasHandles. Becuase an Alias is an arbitrary description of how to find a file you need a higher level API to resolve them.

It is a pity that the Alias Manager doesn't have a plugin API to add more Alias formats for different filesystems. I don't think the full portential of the Alias Manager has been met. 

----

I am using BDAlias to create aliases for use in a persistent store (which btw I checked and it uses methods current as of 10.4, though the site claims no updates since 2002).  This wrapper class allows for getting the raw AliasHandle etc from within the object.  My question is, are aliases unique?  If I create 2 aliases to a file will they compare as true (ie alias1 == aias2 results in YES).  If not so transparently, is there any way to compare two aliases to test for equivalence other than resolving them to their paths and comparing those? 

I have been poring through the FileManager and AliasManager docs and can find no mention at all of alias comparisons.  It seems the idea is just finding the file, not checking to see if the file in question is, say, already included in a playlist.  just as an example.
Using aliases is the only way I can think of to preserve playlist connections despite file relocations, but without this ability the other half of the equation (preventing duplicates) is, while possible, inegelant and ultimately not guaranteed.

Thanks!

---- You may have to test it. Set up a tiny test rig, compare aliases, and see if you can't make a quick isEqual: implementation for BDAlias? -- RobRix

----

You can't compare two aliases without resolving them. Aliases can store different kinds of information, from a simple path, to an inode number, server addresses, filetype/creator, file name, etc. All of this is used when resolving the alias. You can't tell in advance whether two aliases will resolve to the same file or not without trying it. It's possible that two aliases created for different files will now point to the same file (if the user has shuffled things around, etc.), and it's possible that two aliases created for the same file will now point to a different file (if one is created as a basic pathname alias and one is a full-up alias, and then the user moves things around, etc.). These scenarios shouldn't happen often enough in practice for your duplicates checker to go insane, but they mean that you have to resolve the aliases in order to check for equality.

----

Also, even if two alias records resolve to the same file, they may not be equal for the purposes you care about.  For example, one may be a minimal alias and the other may be an alias created relative to some other file or folder.  (Useful for situations like the files referenced by a document file.)  It's really up to you to determine what equality of aliases would mean for your application, and then to implement a subclass of BDAlias that exhibits appropriate behavior for -isEqual: and -hash methods.

By the way:  **Don't forget to override -hash if you override -isEqual:!**  Objects for whom -isEqual: returns YES must **always** return the same value for -hash, though objects that return the same value for -hash can return NO from -isEqual:.  If you put objects with a broken hash method in an NSSet, or use them as keys in an NSDictionary, the collection will get very confused...

----
Thanks all; I am the original poster and this has answered well beyond my needs, thank you very much.  I suppose, then, that logically it would be in error to create an isEqual comparison for BDAlias which would return YES if they were *currently* pointing to the same object because as another poster said this state could change depending on the specific configuration of the aliasRef itself.  Thus you might end up having a duplicate checker discard certain elements which are in fact not the same as a function of time.

Still, I can't shake the conception that creating 2 successive Aliases for the same file should be absolutely identical, no?  They follow the same programmatic process and they are based on the same information exactly.  The catch seems to be that the information used in a comparison is the caveat I put at the beginning: not pulling out internal data.  Since the internal storage may differ conceivably based on the point at which the reference was created (if, say, the same file had a reference made to it before and after a relocation), answering the question "Are these aliases the same" is not equal to "is the information referenced by these aliases the same".

Answer: pull out the internal information.  Which is what I'm currently doing but I had this fool's idea of optimizing my code, lol!

----
Once again, no, two aliases pointing to the same file do *not* have to be identical. Imagine two aliases pointing to the same file: one is a minimal alias which only encodes path information. One is a full alias which encodes the path, server address, inode number, HFS type/creator codes, etc. I'm sure you can see that these will not contain the same data, even though they point to the same file.

----
Well, "creating 2 successive Aliases for the same file" via the "same programmatic process" may indeed create identical aliases.  This would have two be tested, with isEqual and hash.  But since pragmatically this situation is not the exclusive scenario (different creation times being one deviance example, your minimal/full distinction another), it is moot.  I concurred already that they don't "have" to be identical, I was just saying that sometimes they might be.  Once again, to therefore umbrella every situation one would have to pull out the internals, which negates any interest in the equivalence of the aliases in the first place.  Just test the internals, since you have to get them anyway.

