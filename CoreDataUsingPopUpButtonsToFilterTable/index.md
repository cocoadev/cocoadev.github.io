---
layout: page
title: CoreDataUsingPopUpButtonsToFilterTable
---

Describe CoreDataUsingPopUpButtonsToFilterTable here.


I am developing an NSPersistentDocument based player aid for a miniatures game in order to learn Core Data.  I have a Character entity, which has a relationship to another entity called Nationality.  The Nationality->Character entity is to-many.  The reverse is to-one.

I have a tabview item for spotting, and I want the user to be able to select the nationality of the spotter, then have a list of the characters of that nationality either in a table or a popup (a popup would be best actually).  I have another tabViewItem where the user sets up the game (i.e. creates new nationalities for the game, and creates new characters assigning them to the nationalities).  That tab uses two tables, one for Nationality and one for Character, and has the binding set to an array controller for Nationality and another for Character.  The tableview for Nationality is bound to the Nationalities array controller and the character is bound to the Nationalities array controller on selection.characters.

On the sighting tab, the spotter nationality popup's content binding was set to the Nationality array controller, and the contentValues was set to the nationalityName, and selected object was set to the Characters controller as selection.nationalities.  This isn't working.

1.  I want a popup button to list all the nationalities.  I want the user to be able to select one and have that selection provide a filter for populating the other character popup (though I can have a table here instead).  (i.e.  the character popup will only display characters from the nationality selected in the first popup)
2.  I don't want the nationality selected to change anything in the characters (i.e. selecting a different nationality shouldn't change what characters point to what nationality)

As a related matter to #2, for some reason the nationality popup currently is only populated at all if in one of the other tabs, a nationality is selected which contains characters. 

I have tried a variety of things based on various tutorials, but I always seem to have the Nationality popup change the nationality of the selected character in the character table on the other tab.  Obviously, this is not what I am looking for.  I have thought about filter predicates, but they seem to require more code than it seems this should require, as these seems very straight forward in principle to me.  

Any help would be greatly appreciated.

