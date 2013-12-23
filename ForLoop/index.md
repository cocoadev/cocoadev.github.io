---
layout: page
title: ForLoop
---

a basic for loop:

    
int i;
for (i = 0; i < numberOfSongs; i++)   // of interest is that this i++ is *faster* if it is ++i since the language doesn't have to make another copy.  That said, most compilers can optimize the difference trivially.  Supposedly there are some STL situations where it actually makes a big difference, though.
{
  printf ("loop trip %i ", i);
}


while loop:
    int i = 0;
while (i < numberOfSongs)
{
  printf("loop trip %i", i);
  i++;
}

do-while loop:
    int i = 0;
do
{
  printf("loop trip %i", i);
  i++;
} while (i < numberOfSongs);

NSEnumerator:

    NSEnumerator * enumerator = [[NSArray arrayWithObjects:@"1",@"2",nil] objectEnumerator];
NSString * string;
while (string = [enumerator nextObject])
{
NSLog(string);
}


----

*What's Cocoa here? This isn't a C tutorial site*

Honestly, what is the point of this

*don't delete other people's comments because you're having fun listing all the possible C loop constructs.*

So this is what I've been missing all these years!

----

*don't delete other people's comments because you're having fun listing all the possible C loop constructs.*

Comments should be deleted if they do not contribute to the page.  It's a wiki.  I agree that pages should have discussion pages associated with them.

*Pages should be "deleted" if they don't contribute to the wiki. What does this page contribute?*

Something more then these comments.

