---
layout: page
title: SearchablePreferencePanes
---

A searchable preference pane is a System Preferences pane that can be searched by Spotlight (and Tiger's System Preferences search field). All Apple-supplied preference panes are searchable; although no documentation is available (that is, this is internal, private behaviour), making a pane searchable seems an easy task to do.

To make a prefPane searchable:

* add a NSPrefPaneSearchParameters key to the pane's Info.plist whose content is a String such as "MyOwnPrefPane".
* create a file whose name is the string above and whose extension is ".searchTerms". Make it a localized resource (i.e. place it in a .lproj folder) and provide localizations if needed.


The searchTerms file is a property list containing a dictionary whose items are again dictionaries. Each array's key represents an "area" of functionality of the preference pane and is somehow **(todo: investigate how)** passed to the preference pane upon selection; the dictionary contains a single "localizableStrings" key tied to array. The array contains dictionaries that detail the words associated with the function and the string that is displayed in the pulldown list in System Preferences while typing.

For example, the dictionary might have the following structure (taken from Localization.prefPane's own searchTerms):

    

"Formats" = {
    "localizableStrings" = (
        {
            "index" = "Gregorian, Buddhist, Hebrew, Islamic, Japanese"; /* show me when the user types any of these words...  */
            "title" = "Calendar styles"; /* shown in the pull-down in System Preferences */
        },
        {
            "index" = "region";
            "title" = "Regional formats for numbers, dates, times";
        }
           /* ... more items here ... */
    )
};
"InputMenu" = {
    "localizableStrings" = (
        /* and so on and so on */
    )
};


*English is not my primary language - please proofread, correct & investigate, everyone :D* -- l0ne aka EmanueleVulcano

----

I got this working just fine, but does anyone know if SearchablePreferencePanes are compatible with Panther? I know some things are just ignored on previous OS's, but I dont have a Panther system to test this on right now.

-- SuperFreak

*it's just some extra files in the bundle and a key in the .plist, so i don't see why it wouldn't be compatible*

