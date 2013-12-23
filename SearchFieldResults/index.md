---
layout: page
title: SearchFieldResults
---

Hi, Wanted to see if there is any way to do what System Preferences does in Tiger. When you do a search in the search field it brings down a list of results that pertain to the search. How does it do that? Also is there a way to do the spotlight look on the results like the wasy system preferences does when you select the result and a spotlight hovers the icon? Thanks in advanced

----

I'm thinking you probably use a combo box and just shape it like a search field. Anybody else can provide a way to do that?

*Well, no, it'd be easiest to use a borderless window positioned below the control's screen coordinates ... *

Interesting.. can you explain?

See: BorderlessWindow ... Create it, add a list to it, populate the list, grab the screen coordinates of your search control, get the height of your borderless window, position it and show it. Though the shaped combo box might actually be better .... you might have to subclass it and use background images to get the rounded caps on either end. I'd personally use the borderless window approach because the standard search box already has all the built-in support like the x-cancel-button and the pretty little mag glass that can pop up a list of search parameters.

