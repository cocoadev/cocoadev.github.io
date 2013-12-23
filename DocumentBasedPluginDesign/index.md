---
layout: page
title: DocumentBasedPluginDesign
---



I've been having a problem with providing storage for plugins in my document based apps.  Basically, I want to allow loaded plugins to store additional data in a document.  That seems simple enough; I simply add some mutable dictionary to my document class, and reveal it to plugins.  However, various problems arise when other plugin makers decide to use custom classes.  What happens is they add instances of some custom class to the mutable dictionary in the document, and then later on, after they have stopped using the plugin, they try loading the document and it fails, because the class description no longer exists.  This is also an issue I have been having when considering a change over to using Core Data, since it seems to choke when it doesn't have an entity description for something in a data store.

Anyone else have better ideas for this sort of plugin architecture?  One fix to my problem is simply to ask plugin developers to only use standard objects, and not custom classes.  This is fine, but I feel like there must be more elegant solutions (perhaps ones involving Core Data, as I see no way around the problem).

*One thing you could try is making your documents into bundles. This way, each plugin can save a file in the bundle, with the application saving most of the data in a main file. Any data a plugin needs can be loaded out of the plugin's file, if it exists. If the data is not needed, the file will not be touched. --JediKnil*

