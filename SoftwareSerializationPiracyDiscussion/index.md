---
layout: page
title: SoftwareSerializationPiracyDiscussion
---



**The real question is though...is it worth it to stop <1% of the population?**

----

"If we lived in a world where 1% of the population were thieves, would we stop them? Yes of course. **But of course, we'd hope that locking the doors and having a police force wouldn't be a total inconvenience for 100% of the population. Just something to think about. You're a part of the population when it comes to this inconvenience.**

*This is a bad analogy, cause nobody breaks into my house to steal the software I have bought -- the only problem is the potentially lost sale experienced by the original author, and if he sells his software for $10-20, the people making illegal copies most likely would not have bought it anyway.*

----
Pirated serials is the main secure problem for shareware. Why not to link serial number to a system's user name? I mean to that name user uses when he logins? Sure one can leak his serial number with his name, and another one can even change his system user name to use your cool app for free, but if all programs used such system, then it would be much more hard to use pirated serials. Nobody will change his name each time he want to run another program, and you still have all others line of defense, such as blacklists etc.

----

The problem isn't people giving away their license key but that crackers generate fake license keys. Unless you use public/private key encryption then this problem remains, except that they'll then need to distribute the key generator instead of a key, which AFAIK isn't done with the current serial databases.

The downside of this system is that you will get a lot of customers contacting you with problems. And also a customer will only be able to use the program for a single account on his machine.


----
This actually reminds me of a problem I was working on a while ago.  After a lot of poking around in headers I determined that most of the ObjectiveC runtime is in fact composed of structs.  Hence a lot of tedious manipulation of structs and a lot of debugging could allow one to create ObjectiveC elements in memory without declaration in code.  I never really pursued it beyond that though, as I thought it wasn't worth the effort.

----

I've tried something that works for me. I put a list of all of the serials that I've given out on my webserver, using MySQL. The app requires that the user be connected the first time they use the app, in order for it to check (this is after the obvious 30 day trial). This is done after I'm sure that the computer's hosts file hasn't been tweaked with to get around this check. The app will only communicate with what it knows is my server. If it can't get a response, it will simply tell the user to try again later or update to a new version (if the server has changed). After it looks and compares, the server checks to see how many machines have been registered (via ethernet id), and if the number of machines is greater than 3 or 5 (depending on licence), then it says NO. It also writes to a hidden file on the user's computer that they have attempted piracy. Every time they enter another serial, I do this check. If they try to use many more known pirated numbers, then they go on report, and if I'm in a cranky mood, I lock the app up. Pirates have no rights. -- JasonTerhorst

----

Bravo. This last could productively go into a subtopic within ProductSecurityStrategies entitled BlacklistChecking.

What I am trying to do is separate emotion from strategy in this discussion. One's feelings about piracy are not
exactly productive when trying to develop strategies for optimizing the returns on the work one does.

Fine. Then it needs to be mentioned that destroying the files of your users (even the pirates) is evil and is likely to create an enormous backlash. Even ignoring all emotion, it's an incredibly stupid strategy and will do far more harm than good.

----

Well, what would people suggest, to protect your rights, in the event that your piracy prevention system, whatever it is, has been broken? Surely Microsoft or Apple can afford an army of lawyers, but many of us cannot.

*and if you think that way, you're a lot like the owner of the convenience store around the corner from my
place, who charges me a dollar for a 25-cent donut to make up for the fact that he's not Wal Mart, and
buys a $40000 anti theft surveillance system to interdict shoplifting of his overpriced donuts.*

simply suggest that you deal with the fact that your software will be pirated. Even Microsoft, with their billions of dollars and armies of lawyers, have their software pirated to a degree that is simply mind-boggling to a small developer. Piracy is not going to go away just because you wage a small war of revenge on a small number of them. Realize that your goal should not be to reduce piracy. Your *goal* should be to increase sales as much as possible. Reducing piracy is only one way to achieve that goal, and it's only worthwhile if your methods for reducing piracy generate increased sales.

----

I recommend reading http://wilshipley.com/blog/2005/06/piracy.html

----

The **best** way to protect your software is, currently, a serial code + activation mechanism, plus blacklist, plus asymmetrical cryptography (i.e. I have a public key in the bundle, you have a private key on the activation server). This would effectively remove weak points in the registration process, removing the number 1 pitfall for Mac shareware apps -- the ability to steal an app by the means of copying and pasting a simple serial number. This kills most of the piracy, believe it or not -- not enough users are savvy enough to apply cracks, even cracks as easy to produce as InputManager<nowiki/>s. -- EmanueleVulcano aka l0ne

*100% AGREED. This is a VERY effective method. Yes, a knowledgeable individual would have little trouble bypassing it, but the truth seems to be (even for popular apps) that there simply aren't enough sufficiently-knowledgeable individuals out there. My app has not shown up on warez lists, crack sites, etc. since the version prior to implementing this approach. It is **effective**. *

I still maintain that any activation mechanism should be extremely carefully considered before being put into use, because it can cause customers to avoid your product. My personal feeling is that it will not be worth the effort, and that even if it is, it's a fairly wrong thing to do, but I have no evidence of this.

*Understand I'm *not* trying to start an ignorant flamewar, but I *have* done this. My product brings in several thousand a month (many times more than before the activation policy) and it's been well worth the effort. Your mileage may vary, but perhaps one customer per few months complains about activation. Many actually specifically mention in their support requests and feedback that they know it's a 'necessary evil' and have no problem. The response from paid customers has been overwhelmingly positive. I *do* have proof of this after nearly two years with two separate apps (well, the second was released less than a year ago using activation). ;-) Unless you've tried it, please don't speak about user avoidance or how angry users will be. If you don't know, you're only speculating. As to whether *you* would avoid it, that's up to you, but most certainly not the majority.  FWIW, I *do* agree whole-heartedly that it should be carefully considered. I'm also the one who keeps putting back the "Activation is *not* evil, but implementations can be." comment in another related page. If you decide to go with activation, make it lax enough that even a casual pirate (one who gets a friend's serial) can install it on a machine or two, but limit the activations such that a serial number on a warez list will quickly hit its limit and you can blacklist it, contact the original purchaser, or take whatever action you feel is necessary. This gives you activation protection without annoying your users. It's a sane balance.*

What do you mean when you say that the response from paid customers has been overwhelmingly positive? When I see that, I get the image of customers e-mailing you to say, "Oh, thank you so much for putting an activation scheme in your product, it's wonderful!" I have a hard time believing that, which is why I think that I misunderstand.

My experience with shareware indicates that avoiding activation is not a minority position in the least, as you imply. The only non-commercial program which has activation that I know of is EV Nova, and there are tons of non-activation apps out there. You are free to do what you like, but acting as though you're in a comfortable majority of developers does not seem to be in touch with reality.

*Oh boy, here we go. By 'overwhelmingly positive', I mean that when previously-registered users read the e-mail stating the app would be activation-only from that point on (which provided background info, etc. on where the piracy occurred, etc.), *many* wrote back with comments like "stick it to 'em!" and "If it keeps you in business so you can keep improving XYZ.app, I'm all for it." THAT kind of positive. You'd be surprised how many users out there a)know what activation is, b)understand how much piracy can threaten small-time developers' ability and *willingness* to keep working hard on a product, and c)SUPPORT any measure that keeps b from killing or hobbling an app. In other words, there are far more people that indeed *have* written in support of the policy (because it was a lax one - just enough to keep mass piracy at bay) than have written to denounce it. As to being out of touch with reality, *I* am basing all of my argument on first-hand real-world experience. I'm *not* pulling numbers out of my ass or speculating on something I think might happen if I'd go with an activation policy. I am documenting reality in my particular case. As I said, your mileage may vary. I'm not talking about the number of developers using activation - never said that - I'm talking about the percentage of users who support it or don't care versus those who hate it. Please don't accuse others of being out of touch with reality if you have no first-hand experience to back up your own point of view. Not only is it adversarial and rude, but unless you're prepared to accuse me of making up this story, it makes you look like a fool. <edited - I finished a point I forgot to lead into>*

*I see where the confusion lies above. I said, "As to whether *you* would avoid it, that's up to you, but most certainly not the majority." I meant (and should have said), "As to whether *you* would avoid it AS A USER, that's up to you, but most certainly not the majority." Sorry for the confusion.*

You really need to stop taking this so personally.

I write asking for clarification on a point, and explaining why I thought I might have misunderstood, and get "oh boy, here we go" as the response.

I note that developers who do not use product activation, rather than being in the minority as you state, are infact in the vast majority. This is true, as a browse through macupdate.com or any other shareware listing will show you. And yet, my statement of fact gets me attacked, with claims that I have no first-hand experience (what first-hand experience do I need beyond downloading and trying shareware applications, in order to say that apps with activation are not in the majority?), and then this utterly ridiculous notion that *I* am being adversarial and rude, when in fact it is your response which is adversarial and rude.

I'm done with this page, and all the pages related to it. It's clear that everybody's egos are too large to allow productive discussion.

*Once again, you're basing this on the misunderstanding that I'm stating non-activation-using developers are a minority. I clarified above and yet you're still harping on it. If you are to argue a point, make sure you first understand it.*

**
I am neither of the two parties back-and-forthing above about the issue of activation-avoidance. The fact of the matter is that if your app brings in "several thousand a month", you do not stress yourself about the half dozen customers you missed by employing an activation scheme. If, on the other hand, your app brings in < 1000 (euros, dollars, pesos, whatever) over its *lifetime* you may feel differently. The former case is certainly not in the "comfortable majority of (shareware) developers". Emotion is the only thing on display in this discussion. Logic is out the window.

To put it all too bluntly, paraphrasing someone from the other discussion of piracy
*
If you have an overpriced or under-developed product out there which doesn't sell, don't blame it on piracy, when the problem is that the perceived value of the product doesn't match its price.
*

If *either* piracy or activation-avoidance is "hobbling" sales for your app, you have a lot more to worry about than the morality of trust, and in fact what you are contending with is the presence in the world of talented, successful shareware developers.

**

*This attitude and response is a lame, escapist way of attempting to 'win' the argument. It's a pretty big assumption that "it's not piracy, it's a sucky, over-priced application" or that "if you disagree with my perspective then you don't know anything and by the way everyone else disagrees with you too". If that's all you've got by way of argument, you lose all credibility with me. At any rate, this is degenerating into an emotionally-driven flamewar exactly as I figured it would. Argue amongst yourselves; I'm out.*


----
I deal in demo's and full-versions, and am employing multiple serial numbers and/or verification schemes. One asks my customers to always remember the date of purchase, this is relatively easy because of credit card statements etc. Upon registration I check the date for consistency, simple things, like if the purchase date is before my software is released or after it was discontinued. If you make it plain that these registration inconviences are saving the customer money, even $5 or $10 bucks, then they will be more forgiving.

----

To the arrogant dude who sells for thousands (of dollars?) each month: I too sell for thousands of dollars each month, yet I have no product activation, no locking of the app to a single machine, and even the trial period can be extended simply by deleting the preferences. How do you explain that? And as for product activation being required and accepted by customers, this same argument is used for DRM -- you are limiting my rights as a consumer by putting artificial restrictions on what I can do with the goods I purchase. Users will accept a lot, this does not mean it's okay -- currently Hollywood is fighting innovation with laws (DMCA, broadcast flag, several companies have been sued for products which doesn't play after Hollywoods rules, like the DVD jukebox, time-shifted radio etc.), and they are using the same arguments as you. This is an ideological fight, and most of your users don't see the long term consequences of consenting to the rules you impose on them, or don't care (what's that famous quote? those who are willing to give up a bit of freedom for security deserves neither)

(I figured this page is for highly emotional rants, sorry if we were actually having an actual discussion ;) ).

----

I think it's a waste of time to worry about people pirating your programs or distributing serial numbers/keys. 

Bottom-line: you're NOT losing sales because of it...  People who use pirated software or stolen keys wouldn't *buy* your software anyways.  They just wouldn't.  If they can't download your program, find a stolen key, or crack it themselves, they'll just give up and go watch TV. 

They're not going to say: "Darn, I can't find this program on bittorrent; might as well fork over the $10."  
Shareware makes money off honest people.

----

This guy above me is smarter then the rest of you. Pirates for the most part don't buy things. You get the occasional one who will buy something after extended or day to day use, but they are the vast minority in the group. These aren't sales you are losing, they were never sales. A Serial number based on a name, or part of a CC number encoded in it is enough to keep mass piracy at bay. Did anyone ever think to ask these pirates? Perhaps get their thoughts on the subject?

