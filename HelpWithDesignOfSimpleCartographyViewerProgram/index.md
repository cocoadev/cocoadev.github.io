---
layout: page
title: HelpWithDesignOfSimpleCartographyViewerProgram
---

Hi all.

I have been learning Cocoa (slowly in between school and a new baby) and have made some progress. Things are coming together. I want to develop a larger project now that I have finished some tutorials and the thing I most want/need is a map viewer. I work in the field of geographic information systems (GIS) so this would have some real value for me. 

I have toyed with data storage via SQLite and have that functionality down using an SQL interface. Now I would like to be able to display the map geometry objects. Here are example inputs as defined by generally accepted standards:

point (x y)
linestring (x y, x y, x y, ...)
polygon (x y, x y, x y, ...)

So at this point I can more or less (still working on it) retrieve the geometries and put them into NSBezierPath. Where I am getting lost is conceptually how to handle these things. The situation would be that there are several geometries having the same display attributes: line size, line color, fill color. I thought about hitting the database for each legend class, for example, all interstates have the same attributes of linesize a, linecolor black, then hitting it for the next legend class, secondary roads, line size b, linecolor red. At each hit an NSBezierPath is created for all those objects. But this seems like it would hit the database too much and slow the display. Then, instead I thought it would be nice to cache the display somehow so that the user could zoom in and out as desired and avoid the time it would take to build all these paths. So now my thinking is to create the map display once and store it as a pdf in memory. Any comments on this approach and how to implement it?

Additionally, I have been using ChartSmith for making charts and really like the features and ease of use. I would like to implement some of its features, but can't find any examples or tutorials for these features:

1. There is a background that can be a solid color, gradient, or image. This is also resizable so that in my program the map background (as an NSView?) could be adjusted to a generally used size independently rather than having to adjust the window size. 

2. In ChartSmith the chart is floating inside the view and can be moved/resized and have its own background of color, gradient, or image. Is this a subview "chart" of the superview "background"?

3. In ChartSmith the legend also has the behavior of the chart: floating/moved/resized/etc. Another subview?

4. I would also want a title with this same behavior. Even another subview?

Any help to get me started in the best direction would be great!

Philip

----
One possibility could be to use something like AquaTerm as an output device. 

----
Philip,

If you wouldn't mind a little collaboration I would be interested in helping you with your quest for a custom MapView. 

For me, my preferred geospatially-aware database would be PostgreSQL/PostGIS to store all the vectors and their attribution. PostgreSQL and PostGIS are open source and are available on just about any existing operating system currently fielded.

As for the custom view itself, it would have to have the ability to perform reprojections of coordinates on the fly to allow it to display geographic data of disparate projections and datums (horizontal mostly, but also vertical). 

If you wanted to include a digitized map as a background for the view, it would best if the image was GeoTIFF image that contained the projection information from the original paper map source (if possible). With the projection information you can 'warp' the image to better line up with any projected vectors.

Weldon

----

I would love collaboration! Unfortunately, I haven't had much free time lately, so I haven't been investigating this recently. I agree with GeoTIFF (I filed an enhancement request to Apple for the ability to read the GeoTIFF header from NSImage, if you are interested you should do the same). Given CoreImage, I think this is a very important ability to extend MapView into remote sensing data display, as well as my personal preference for spatial modeling using gridded data.

I wouldn't want to limit the vector format to just PostGIS (after all, MySQL also has a OGIS extension). I think the MapView should take an object in OGIS well-known-binary or well-known-text format for compatibility. Given the wide use of Shapefiles, the underlying model should be able to convert the shapefile shape into well-known-binary format or the MapView should be able to render a shapefile shape. Also, with CoreData, well-known-binary could be stored in SQLite.

My experience with ArcGIS has caused me to dislike reprojection of data at the view level. I think this is something that should be dealt with at the model level so that spatial data are correctly georeferrenced for both display and analysis. I have seen too many people assume that because layers are correctly aligned for display, they are also aligend for analysis (they are wrong). I thought reprojection could be done by PostGIS anyway? At the least, this should be an option to turn on or off by the developer so that if the tool is for display it could be used, but for analysis not used (the user would then know the spatial information is not aligned and would need to reproject it.

Feel free to discuss this with me. (priggs at cnr dot colostate dot edu). I have some ideas how to proceed, but need some time to implement them.

Philip

