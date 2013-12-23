---
layout: page
title: CocoaBindings
---



Cocoa Bindings is a collection of technologies you can use in your applications to fully implement a Model-View-Controller paradigm where models encapsulate application data, views display and edit that data, and controllers mediate between the two. Cocoa Bindings reduces the code dependencies between models, views and controllers, supports multiple ways of viewing your data, and automatically synchronizes views when models change. Cocoa Bindings provides extensible controllers, protocols for models and views to adopt, and additions to classes in Foundation and the Application Kit. You can eliminate most of your glue code by using bindings available in Interface Builder to connect controllers with models and views.


----

According to Apple:

"The Cocoa controller layer helps you build better applications by providing an infrastructure that connects an application�s data with its user interface. In addition to providing the glue between these two parts of an application, the controller layer helps you build polished, easy-to-use applications that leverage object relationships, provide sortable tables, and include intelligent selection management."



----

There are some interesting examples of Cocoa Bindings here


Related topics: [Topic]

These technologies are all related, and appear to be called "the Controller Layer" or "Cocoa Bindings".
Now, can someone explain the whole thing? Please?

----

The whole thing is a set of layers.  From low level to high level:  KeyValueCoding -> KeyValueObserving -> NSController -> NSBindings.   NSValueTransformer is mechanism implemented within the Foundation that allows values to be transformed from one type to another.   A value transformer (or multiple transformers can be chained) can be applied to a binding such that conversion happens whenever data passes through a particular binding.

KeyValueCoding lets you manipulate properties (attributes or relationships) of objects easily.  KeyValueObserving lets you find out when a property in another object changes easily.  KeyValueBinding lets you link the value of a property in one object to the value of a property in another object.  NSController uses KeyValueBinding to let you do this easily in InterfaceBuilder.
[http://www.tokobungasabana.com Toko Bunga Online] | [http://www.propertykita.com/rumah.html Rumah Dijual] | [http://www.vincentmaestro.com Rental Sound] | [http://www.kindercube.com Perlengkapan Bayi] | [http://www.raywhitesemarang.com Properti Semarang] | [http://www.forklift.co.id Rental Forklift] | [http://www.pedatimotor.com Aksesoris Sparepart Motor]
----

Here is my take on it:

A model object may have some attributes, e.g. "DocumentName" accessed thru setDocumentName: and documentName (i.e. KeyValueCoding). Furthermore, if the model change this name internally, it should issue a will/didChangeValueForKey:@"DocumentName" (to conform to KeyValueObserving).

This allow others to observe the value of this attribute, and is exactly the job of NSObjectController, it will observe a value (attribute) in the model, and propagate any changes to the view, potentially transforming the value using an arbitrary value transformer (object) -- the link is bi-directional, so if the view is changed, the same will happen in the other direction (i.e the change is propagated to the model).

Those familiar with the 12 year old MagicUserInterface GUI kit for AmigaOS, will probably recognize this concept :)

I have not been able to fully understand the inner workings of NSArrayController -- by binding the 'value' of an NSTableColumn (which is not an attribute nor mentioned in headers or the documentation) one suddenly have the array controller act as datasource for the entire table view, even handling sort. To me this does not seem like the usage of a flexible API, but instead some very hardcoded NSTableView support in the NSArrayController.

Apart from the above, the new binding API has also made it possible to bind arguments to the action selector sent to a views target -- target arguments are also attributes 'fetched' from an arbitrary object, and if any of the arguments are unavailable, the view will disable itself.

There seems to be some bugs or unwanted behavior in this binding API, for example if multiple empty rows are selected (in a table view controlled by an array controller), the (other) views connected to the array controller will see it as if only one entry is selected. Also, if an attribute returns the empty string, this will be seen as the attribute is unavailable for the argument binding, thus disabling the view.

