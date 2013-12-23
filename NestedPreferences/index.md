---
layout: page
title: NestedPreferences
---



I have a program which stores preference data in arrays, which are stored in two dictionaries, which are stored in yet *another* dictionary.  To wit:
    
{
    Addresses = {
        Allow = {
            "cocoa@alpha.example" = (
                "alice@beta.example",
                "bob@gamma.example"
            ),
            "dev@delta.example" = (
                "carol@epsilon.example",
                "dave@zeta.example"
            )
        },
        Deny = {
            "nested@eta.example" = (
                "eve@theta.example",
                "fred@iota.example"
            ),
            "preferences@kappa.example" = (
                "gail@lambda.example",
                "harry@mu.example"
            )
        }
    }
}

My program needs to add or remove stuff from the deeply-nested arrays, but the only way I've seen to do that so far (using NSUserDefaults or even CFUserDefaults) involves taking out the *entire* "Addresses" dictionary and putting it in an NSDictionary, modifying that, and writing the whole thing back.  Is there (maybe, possibly) any way to just get at the arrays without dealing with their enclosing collection?  Thanks; any help is appreciated.

----
Nope, you have to do it the hard way.

----
Sigh... that's what I was afraid of.  Oh well.  Thanks.

----

If at all possible, this should be a class with the appropriate methods which implements NSCoding and so can be freeze-dried to user preferences. Failing that, you might want to write helper functions that do all that for you, e.g.

    
void SetEmailAddressAllowed(NSString *email, flag);
BOOL IsEmailAddressAllowed(NSString *email);
/* etc */


----
Another nice pattern for this is to create a class which directly represents your     Addresses key (and other classes for any other data structures you have), then give that class     -plistRepresentation and     -initWithPlistRepresentation: methods. Then it's a simple one-liner to instantiate it from NSUserDefaults, and another one-liner to put the changes back in.

----

Of course, if you're only making changes to the table in a single function, it may not be worth the time to factor it out at all. Wait, don't throw rocks at me! I only say it because, if one function already does this work, it's probably already *de facto* factored out. :)

----
(It's the OP again.)  I like those ideas.  I had thought about making it some sort of class, but those are both more elegant ways to do it than I had thought of.  It's only in one method, but it might be worth getting into a class considering how ugly that method is.  Thanks for the ideas!

----
Another reason to go with a class (or even a series of classes that nest) is that it makes the structure of your preferences explicit, whereas a method that processes a plist/dictionary only implies the structure. Explicit means better self-documenting code.

