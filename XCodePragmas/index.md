---
layout: page
title: XCodePragmas
---




Some documentation and Apple source examples reveal that General/XCode seems to support some helpful pragmas.

The most notable of these is the pragma "mark". This pragma allows developers to suggest a description for groups of methods. This mark pragma most noticably affects the method finder menu at the top of each editor field. For example:

<code>
#pragma mark Drag Helper Methods
</code>

Would place a mark in the menu, and subsequent methods will appear beneath it. It also seems that you can place a blank line in the menu (to help grouping) by making your mark just a dash, like so:

<code>
#pragma mark -
</code>

Also, General/XCode interprets

<code>
#pragma mark - My Cool Group
</code>

as

<code>
#pragma mark -

#pragma mark My Cool Group
</code>

With these rules and the default indentation scheme, the menu becomes much easier to rapidly scan through. You can see a good example of this in 
file:///Developer/Examples/General/DiscRecording/General/ObjectiveC/General/EnhancedDataBurn/General/AppController.m

You can also use more than just a dash. You can put any text you want in the pragma and it will show up in the fuction reference pop-up menu.
    
#pragma mark Class methods
#pragma mark Public object methods
#pragma mark Private object methods


If you have large files, this can be very handy.

Does anyone want to list other helpful pragmas if there are any?
-- General/DaveFayram

As a side note: #pragma mark is new in PB2, IIRC. General/CodeWarrior also had that (or maybe it was MPW) and I really missed it in PB. Proof that Apple *do* listen to programmers' requests, so keep filing those bugs! :-) --General/UliKusterer

Another side note, Xcode also accepts, e.g.:
    
// MARK: Class methods
// MARK: -

As an alternative to #pragma mark - General/ChristopherLloyd , credit goes to General/JensAyton for pointing this out
----

<code>
#warning !FIX! only one element and Document Type allowed
</code>

Not a #pragma but this creates a compiler warning. It's often more useful than e.g. "fixme" comments because of the increased visibility.

----

There's also

#error blah

which comes in useful inside preprocessor conditionals:

    
#if TARGET_RT_MACHO
#include "General/MachoDefinitions.h"
#else
#error Unsupported runtime - No customized definitions available
#endif


and there is

    
#pragma unused(varname)


which lets you suppress the "unused variable" warning for dummy parameters.

--General/UliKusterer

----

Though the most portable way to supress the "unused variable" warning is to do this:

int foo (int bar)
{
  (void)bar;
  return 0;
}

then you won't get warnings about 'unknown pragmas'.  See this thread more a long discussion:
<http://lists.apple.com/archives/carbon-dev/2005/Dec/msg00475.html>
-- smcbride
