---
layout: page
title: FlippingViews
---




**Summary**

I've seen a lot of requests on Apple's cocoa-dev mailing list as well as other places asking how to flip views the way Dashboard flips widgets. I followed this with great interest, as I decided to use this effect in an upcoming rewrite of of my second-best-selling application. 

Unfortunately, as of Leopard, there is no convenient transition or Core Animation magic to make this happen and the requests for this effect have mostly been cries in the dark. Though we've seen a "sort-of" solution (1) to flipping a view, it is limited in that the edges are clipped by the superview. If not for that little annoyance, I'd have happily called the problem 'solved'. 

Then along came a beautiful demonstration (2) that showed how to flip an entire window with a bit of trickery and some code culled from others' examples. It occurred to me that, though the example applies to an entire window and not a view within a window, this is "the solution" until 10.6 hopefully adds a view flipping mechanism.


**The Solution**

It seems laughably simple. Silly, even. :-) Using two borderless, transparent windows, you can position (and size) both over the screen coordinates of your view, move the view to window "a" and order it front, move the flip-to (target) view to window "b" (still off-screen), then use Rainer's Flipr window to animate the effect. Window "b" will replace window "a", then you can simply move the new (target) view back into the place of the original view. To the user, it is seamless and looks perfect.

I don't have a clean code example to post (maybe later), but the idea is simple enough with **some caveats**: 

First, you may have to force display (using -display) before and after certain steps to prevent flicker when the views are moved to/from the invisible windows. 

Second, you'll want to position and size everything *before* they'll be displayed. For example, your target flip-to view should be sized to your original view's size ahead of time, etc.

Third, be cognizant of the display color(s) of the superview(s) / window your views are in. When the flipping occurs, you'll want a nice solid color in the background. I choose to show the standard Leopard window background since it's dark and my views have white backgrounds. It sets off the edges of the animation nicely. I originally used a white background but it just didn't look as pretty.

Fourth, if you're not using GarbageCollection, you'll want to make sure you properly retain/release your views while swapping them around. Newbies beware. :-)

Fifth, there are some times when the animation slows down. WAY down. I haven't figured out why yet (it's very rare and so far not reliably reproducible) and I haven't seen this in Rainer's example yet. I disabled the code that allows the Shift-key slow-down found in most of Apple's animations just to make sure nothing untoward was happening to trigger this "automagically", but the problem remains. I have not yet done any real testing yet, but I'm sure this is my fault.


**References**

1 - "Noodlings: Animation in the Time of Tiger: Part 3" - http://www.noodlesoft.com/blog/2007/09/20/animation-in-the-time-of-tiger-part-3/

2 - "Rainer Brockerhoff's Source Code (see Flipr exampe)" - http://www.brockerhoff.net/src/index.html

----

**Discussion**

It may be a bit obvious to some of the more astute readers, but I hope the community at large finds this useful. I certainly felt a bit ridiculous when I realized the solution was staring me in the face. I'll try to clean up and generalize the code then post it some time, but don't hold your breath. ;-)

 - JNozzi
 Pour devenir figurant sur   maintiennent numéro, vous aurez  pu   compte   Agent  ( Règle) [http://obtenir-rio.info numero rio]. Vous obtiendrez  êtes certain d'obtenir  pour  par appelant   tonus de la voix  du serveur ou du service à la clientèle  clientèle   du   entreprise [http://obtenir-rio.info/rio-bouygues numero rio bouygues] . Vous ne  certainement  acquérir  un SMS avec votre . Avec  votre actuelle [http://obtenir-rio.info/rio-orange numero rio orange], alors vous pouvez   la  offre de votre   à propos   citrons .

