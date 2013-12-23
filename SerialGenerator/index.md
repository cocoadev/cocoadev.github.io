---
layout: page
title: SerialGenerator
---

I'm trying to build a number generator that creates 16 character serials by using input from a NSTextField. So I can create SN for my app,
For Example

*Name:Bob Smith
*SN: XXXXX-XXXX-XXXX-XXX

Any Help.


Are the X's digits and/or letters?  Are they derived from the name (e.g., hashing)?  Using some random number generation routine might be useful.

*seriously, if you need help with this stuff, you probably should not make your application shareware! :-)*

----

A simple way to do this would be to generate a hash of the customer name, combine this with a secret number, then generate a hash of the resulting data. This new hash (which includes your secret number) is then used as the SN/key for the app. Inside the app you can regenerate the SN/key by performing the hashing process again and checking to see if the hash you compute matches the hash that the user has entered.

Of course, your *secret* number must be embedded within the application in order to verify the SN/key in this way. You could make the process more secure by making your app establish a network connection to your server (which has the secret number instead of the application) and ask it to verify the SN/key.

There are many different ways to do this sort of thing. If you are looking for a more secure method, you might explore using RSA/PublicKey encryption techniques.

Fundamentally, any code that runs on a client computer is totally untrustworthy as far as security is concerened, because there is nothing stopping the user from re-writing the code or using a debugger to examine the internal processes for weaknesses.

