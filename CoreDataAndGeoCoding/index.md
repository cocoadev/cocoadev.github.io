---
layout: page
title: CoreDataAndGeoCoding
---

Geocoding is the taking of a street address (including city, state and ZIP (for the USA)) and generating latitude/longitude values. Yahoo and Google (and others) provide geocoding web services to perform this translation for you. I have a data model where I have business information which includes the address, city, state, ZIP code and latitude and longitude (plus other information not needed for geocoding). I have built the basic CoreData application such that I can type in all the information by hand and all the information is stored correctly and can be edited, etc. What I would like to do is use KeyValueObserving to monitor address, city, state and zip and when all are populated, trigger a web service call to my preferred geocoding service and place the resulting latitude and longitude values into these fields of the currently selected object without having to manually type this information. Ultimately, I would like to make the latitude and longitude fields read only and only populate them with data from the geocoding service sent asynchronously so that they are filled in as I continue to type other information for the business (there's a lot of information to enter beyond the address, etc). 

My problem is that I have not been able to find a place to put KeyValueObserving so that I can make this happen. I had thought to subclass NSArrayController and add the KeyValueObServing there but that didn't seem to be the right place. I thought perhaps I could subclass NSManagedObject and place it there but that too didn't seem quite right. Any thoughts on where I should place the KeyValueObserving?

