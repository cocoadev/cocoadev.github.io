---
layout: page
title: SigtrapKillsProgram
---

I've been working on this program, and things have been going pretty steady, and I generally test my methods before moving on to the next. I did so, and then added 2 more methods, and when I tested the code, I got a sigtrap. Not cool. I comment out those 2 new methods from where they were executed, and I still get the sigtrap, meaning something is wrong with my old code, which tested fine before. Now, I understand what a sigtrap is, and that to stop it, I have to fix something with my retains/releases. The sigtrap comes from NSPopAutoreleasePool, so I think it's safe to assume that something is being autoreleased when there are no more (but I these are more Signal 10 and 11 problems). Worse, I can't debug the sigtrap easily because it isn't called in my code, it's in the autorelease code. Any help?

This seems to be the offending code... what's wrong with it?
    
- (void)setString:(NSString *)newString
{
	if (m_Text != newString) {
        [m_Text release];
        m_Text = [newString copy];
	m_Length = [m_Text length];
    }
}

it was generated using accessorizer, except for the m_Length part....

*I've had this problem myself once or twice, and its almost always caused calling     autorelease on an object that is later released manually. This means, as you've already guessed, that     NSPopAutoreleasePool() attempts to release an object that no longer exists.
The excerpt you've presented doesn't seem to have any problems with it that I can see... the only question I'd ask is whether you assign an autoreleased object to the instance variable     m_Text anywhere else in your code.*

----

Well, I eventually fixed it by just fiddling with the offending code. When this project is done, I'm definitely going to cite you guys, since this is my first major Cocoa project, and I've gotten out of a lot of jams because of you guys. --BobInDaShadows

----

Which offending code? The Accessorizer-generated code above looks fine. If you changed that, you're probably leaking now.

