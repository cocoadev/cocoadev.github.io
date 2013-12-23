---
layout: page
title: ForgiveMeStillANewb
---

Describe ForgiveMeStillANewb here.


simply trying to grasp this concept.

when i declare a BOOL variable as an instance variable, and then create an accessor method to change it, when i go to check that it was changed, it is NOT changed...

I.E.

@interface Creator : NSObject

{

    BOOL fileMade;

}


------------


@implementation Creator


-(BOOL)fileMade

{

    return fileMade;

}


-(void)setFileMade:(BOOL)result

{

    fileMade = result;

}


-(void)makeFile // assume this is invoked.

{

    [self setFileMade: YES];

}


---------
//in another class instance:

if ([creator fileMade] == YES)

  //do something.... but it never does.


do i need to do some type of retain on the fileMade to get it to work.... i have no idea what i'm doing.

----

I think we need more info. Show us more code, such as the call to     -makeFile, etc.

----

make file is actually called as a result of an IBAction, along with some other code.  I know the call works because the rest of the code in makeFile executes fine.

also, if i change the logic to

if ([creator fileMade] == NO
  
it will execute the code that follows.  Almost like the fileMade variable forgets that it gets set to YES.... is that possible??  

----

Please see HowToUseThisSite. Pay particular attention to TextFormattingRules and the bits that talk about giving your pages a descriptive name. MailingListMode would also be a good read. Once you've done that, check out CommonProblem, and particularly the linked IBOutletNSViewIsNil page, which describes a similar problem.

