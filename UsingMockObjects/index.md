---
layout: page
title: UsingMockObjects
---

I am interested in using General/OCMock but there is little documentation and I am unsure of how to begin.  I have been using General/OCUnit successfully.  The reason I think I need to use mock objects is that I am designing an object that will function in response to user input.  I want the object (functioning as a controller) I am going to test to present some choice to the user, then respond to the user's choice by manipulating model objects.  I want to be able to tell the object what the user's choices were and then test what it will do based on these choices.  I think I need a General/MockObject to substitute for the view object.Can anyone point me to a useful tutorial or at least post a few hints?  ----Martin Fowler has a very good article on Mock Objects here:http://www.martinfowler.com/articles/mocksArentStubs.html Basically, you're right - if you want to test that a controller properly interacts with a view - without actually using the view, than Mock Objects are a good way to go.--General/TimHart!
----
Original Poster (or anybody else): Have you had success?  Anybody care to write up a brief HOWTO for General/OCMock?  I'm in the same boat.
Or, how about a more concrete question: I'm trying to mock an object that is a delegate for another object, to make sure that the appropriate messages get passed to the delegate.  I have an informal protocol in the object that sends the delegate messages (the protocol is on General/NSObject).  I don't have any classes that "implement" this informal protocol - that's for people who use my framework to do.  So how can I create a mock object that responds to the message?  When I try something like:
    
  // I've declared an informal protocol in General/MYObject.h on General/NSObject, called General/MYInformalProtocol
  General/MYObject *mo = General/[[MYObject alloc] init];
  General/OCMockObject *delegateMock = General/[OCMockObject General/MYInformalProtocol];

  [mo setDelegate:delegateMock];
  General/delegateMock expect] myDelegateMessage:mo];

  [mo myMessage];

  [delegateMock verify];

I get an error: <code>error: -[[[MYTestTarget myTestName]: *** -General/[NSProxy methodSignatureForSelector:] called!</code>.  I assume that this is because General/MYObject checks whether its delegate responds to myDelegateMessage before sending the message.  Is there a good way to do this?  Do I need to create a formal protocol for classes to use as a delegate??
----
A request for General/OCMock (posted here, because I don't see any way to contact the author on General/OCMock's site): allow andReturn: (or some similar method) to return BOOL values.  NO works (by chance, because 0=NO=nil), but YES doesn't work.  Generally, it would be useful to andReturn:YES, and specifically when I'm using delegates.  I cannot mock a delegate object because the code checks whether or not the delegate respondsToSelector:, which needs to return YES, which I can't do in my mock object.  So I can't test code that has delegates with General/OCMock (at least not test whether they properly call the delegate).
----
Values such as YES are supported but you have to specify them with OCMOCK_VALUE, eg. <code>General/[myMock expect] andReturn:OCMOCK_VALUE(YES)] foo]</code>
----
Actually, that won't *quite* work. You cannot pass something other than a variable into <code>OCMOCK_VALUE</code>. So the boolean would have to look like this:
    
BOOL no = NO;
[[[myMock expect] andReturn:OCMOCK_VALUE(no)] haveYouGotAnyLimburger];

----
To the above poster, you have to get the mock object as follows: 
    
[[OCMockObject *delegateMock = General/[OCMockObject mockForClass:General/[NSObject class]];

----
The General/OCMock homepage at Mulle Kybernetik now has a how-to and contact details, even a mailing list.
