---
layout: page
title: BindingAnArrayControllerFromTwoNibFiles
---



I'm still a beginner in cocoa but i'm getting close to what i'm searching to do.
I've made a project with a button allowing me to add several subviews in a view. Each subview load a nib files for its UI and it works fine.
Now i'm trying to collect the data in an ArrayController using bindings and it's almost working. The only way i found is to duplicate the ObjectController and ArrayController in each nib file to allow me to bind in each one. Well.. here the project :

http://www.langues-vivantes.u-bordeaux2.fr/SD/binding2nib.zip

There is two buttons on the window but i will merge them later.
As you see, when i add a new element, is title is always the first element of the array. I want title1 on element1, title2 on element2 and so on.
The textfield on the main window is binded exactly the same as the other ones but this one shows the last title. I'm lost.

Thanks

----

You might do your friends who do not have a broadband connection a favor by removing the build products from
your project before distributing it, and note that the download itself is an Xcode2 project. I (who do not have Xcode2)
had to construct a new project from scratch. Trying to see what you are up to is almost more trouble than it's worth.

It looks like the content of the object controller in MainMenu nib is Plan-Controller, and the content of the object
controller in Element.nib is Element-Controller. You have an array controller in each nib file, each of which has a
different set of attributes, but which each include properties.title as a key.

Perhaps when you add an element to the view, you may need to do some manual binding for the objects in the view
that you add through the button action - but so far it looks like you want each new UI element to display the same array.

Obviously if you want to change the content and display a different array in each new UI element, you are going to
have to do some more work.

��> *ahh well.. i though i did it.. well it was 1.7MB and now it's only 56kB.. sorry*

----

It occurs to me to wonder what would happen if you added @count to the list of keys in the array controller that does
not include it. But I am really shooting from the hip there...

But I think your main difficulty is that each element you add to the view has its content bound to the same array object.
It will be a nice trick when you can bring it off.

----

Hmm hmm.. well.. i think i got your point : i have to deal with only one ObjectController in the main.nib and manually bind each new element to it, right?

Edit : in fact i would like an array of elements with a dictionary for each element, holding the properties

----

Sooooooo... could you not simply create a new array whenever you create a new view element and manually bind it...?


----

I'm going to do this. You just replied to my question : i didn't know if i had to make it manually or not. My questions might sound dumb for you guys but believe me, we are many cocoa starters here, and we are all a bit lost. Thank for your help.

