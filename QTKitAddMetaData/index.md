---
layout: page
title: QTKitAddMetaData
---

I am trying to add cover art to an mp3 or an aac file. I am using the QTKit on Tiger but of course have to use C in order to use the metadata api's. I see QTMetaDataAddItem but I have no idea how to use this method. I am not very proficient on using C. Can anyone help?

----

Well i guess i have to try to answer my own question. This is what i have so far but it doesnt work. Any Ideas? Please help!! Thanks,

    
QTMovie *theMovie = [theMovieView movie];
	
	NSImage *theImage = [imageFileImageView image];
	NSData *imageData = [theImage TIFFRepresentation];
	const UInt8 *imageBytes = [imageData bytes];
	
	const UInt8 *key = "covr";

	Movie movie;
	movie = [theMovie quickTimeMovie];

	QTMetaDataRef movieMetaData = malloc (sizeof (QTMetaDataRef));
	QTCopyMovieMetaData (movie, &movieMetaData);
	QTMetaDataItem anItem = kQTMetaDataItemUninitialized;
	
	QTMetaDataAddItem(movieMetaData,
					  kQTMetaDataStorageFormatiTunes,
					  kQTMetaDataKeyFormatiTunesLongForm,
					  key,
					  sizeof(key),
					  imageBytes,
					  sizeof(imageBytes),
					  kQTMetaDataTypeBinary,
					  &anItem);


I think I am getting closer to the answer.. the code on top has been updated to what I am working with so far. Please help! Thanks in advance!

----

This question is rather off-topic for CocoaDev. Although you're using QTKit, your main question is on the subject of the C APIs. While you're welcome to keep posting, you may enjoy more success in a more appropriate venue, such as Apple's quicktime-api mailing list. If you haven't already, I'd also recommend searching Apple's sample code to see if they've posted anything that uses these APIs.

----

Thank you for answering. I did look at the quicktime api list on apple's site but nothing there helped. Does anyone know of a good site for quicktime api's such as Cocoabuilder or CocoaDev?

*Did you send an email to the mailing list? I'm sure someone there can help you out.*

----

Here's info on retrieving the cover art.. and it looks like it returns a PNG. Maybe you need to not use TIFF but PNG data? http://lists.apple.com/archives/quicktime-api/2005/Jul/msg00078.html

----

Thanks guys for answering. I know how to retrive the art. I want to know how to set the artwork. This is an ongoing discussion that hasnt been solved and I want to post the answer on this post. Thanks!

----

A coule of comments. First, you do not need to malloc space for the QTMetaDataRef. QTCopyMovieMetaData will allocate any space it needs and then return to you a copy of the movie metadata. You do however need to release that copy when you are done with it.

Second, I think you need to try the 'artw' key, not 'covr'. That is, kQTMetaDataCommonKeyArtwork and kQTMetaDataKeyFormatCommon. 

Does any of this help?

----

This doesnt return errors anymore. It looks like it works now but I dont see the artwork being used in iTunes. If you have any ideas on how i can get this to work that would be great.

    
QTMovie *theMovie = [theMovieView movie];
	NSImage *theImage = [imageFileImageView image];
	NSData *imageData = [theImage TIFFRepresentation];
	const UInt8 *artwork = [imageData bytes];

	Movie movie;
	movie = [theMovie quickTimeMovie];
	
	QTMetaDataRef    theMetaData = NULL;
	ByteCount        size = 0;
	OSType           key = kQTMetaDataCommonKeyArtwork;
	
	OSErr err = QTCopyMovieMetaData(movie, &theMetaData);
	if (noErr == err) {   
		OSErr err = QTMetaDataAddItem(theMetaData,
				              kQTMetaDataStorageFormatQuickTime,
						kQTMetaDataKeyFormatCommon, 
					       (const UInt8 *)&key, 
			           		sizeof(key), 
					       artwork,
						size,
						kDataTypeJPEGImage,
					       NULL);
		OSErr err1 = UpdateMovie(movie);
		
		QTMetaDataRelease(theMetaData);
	}


----

Hey, thanks for figuring this out!!! I've been wanting to do this for some time now. I got your code to work, but your problem is you are giving QTMetaDataAddItem() a pointer to TIFF data and not JPEG data. Look at NSImageToJPEG for help on getting JPEG data from an NSImage. If you have jpeg files already on disk than all you have to do is get an NSData object of this file:

    
NSData *jpegData = [NSData dataWithContentsOfFile:/path/to/your/jpeg"]; 


and then set your     artwork pointer to point to     [jpegData bytes]. --zootbobbalu

Oh yeah, I think QTMetaDataAddItem() needs the length of the image data. 

    
OSStatus QTMetaDataAddItem (
    QTMetaDataRef inMetaData,
    QTMetaDataStorageFormat inMetaDataFormat,
    QTMetaDataKeyFormat inKeyFormat,
    const UInt8 *inKeyPtr,
    ByteCount inKeySize,
    const UInt8 *inValuePtr,
    ByteCount inValueSize,
    UInt32 inDataType,
    QTMetaDataItem *outItem);


    inValueSize should be set to [jpegData length]

----

We are getting closer I think. With the changes you said to make I still do not have it working. Is it working for you? Here is the code I have now.

    
QTMovie *theMovie = [theMovieView movie];
	NSImage *theImage = [imageFileImageView image];
	NSArray *representations = [theImage representations];
	NSData *imageData = [NSBitmapImageRep representationOfImageRepsInArray:representations usingType:NSJPEGFileType properties:nil];
	const UInt8 *artwork = [imageData bytes];

	Movie movie;
	movie = [theMovie quickTimeMovie];
	
	QTMetaDataRef theMetaData = NULL;
	OSType key = kQTMetaDataCommonKeyArtwork;
	
	OSErr err = QTCopyMovieMetaData(movie, &theMetaData);
	if (noErr == err) {   
		OSErr err = QTMetaDataAddItem(theMetaData,
                                    kQTMetaDataStorageFormatQuickTime,
                                    kQTMetaDataKeyFormatCommon, 
					 (const UInt8 *)&key, 
					 sizeof(key), 
					 artwork,
					 [imageData length],
					 kDataTypeJPEGImage,
					 NULL);

		OSErr err1 = UpdateMovie(movie);
		
		QTMetaDataRelease(theMetaData);
	}


----

Now the only problem that I can see is how you are getting a JPEG from an NSImage. 

    
	NSImage *theImage = [imageFileImageView image];
	NSData *tiff = [theImage TIFFRepresentation];
	NSBitmapImageRep *bir = [[[NSBitmapImageRep alloc] initWithData:tiff] autorelease];
	NSArray *bitmapImageReps = [NSArray arrayWithObjects:bir, nil];
	NSData *imageData = [NSBitmapImageRep representationOfImageRepsInArray:bitmapImageReps
                                                               usingType:NSJPEGFileType
                                                               properties:nil];


--zootbobbalu

----

I have added your changes but it still doesnt work. Can you send me the project that is working for you? My email is magicmanpepe [at] gmail [dot] com. Thanks!

Here is the code with your changes:

    
NSImage *theImage = [imageFileImageView image];
	NSData *tiffData = [theImage TIFFRepresentation];
	NSBitmapImageRep *tiffBitmap = [[[NSBitmapImageRep alloc] initWithData:tiffData] autorelease];
	NSArray *bitmapImageReps = [NSArray arrayWithObject:tiffBitmap];
	NSDictionary *properties = [NSDictionary dictionaryWithObject:[NSDecimalNumber numberWithFloat:1.0] forKey:NSImageCompressionFactor];
	NSData *imageData = [NSBitmapImageRep representationOfImageRepsInArray:bitmapImageReps
																 usingType:NSJPEGFileType
																properties:properties];
	const UInt8 *artwork = [imageData bytes];
	
	QTMovie *theMovie = [theMovieView movie];
	
	Movie movie;
	movie = [theMovie quickTimeMovie];
	
	QTMetaDataRef theMetaData = NULL;
	OSType key = kQTMetaDataCommonKeyArtwork;
	
	OSErr copyError = QTCopyMovieMetaData(movie, &theMetaData);
	if (noErr == copyError) {   
		OSErr addItemError = QTMetaDataAddItem(theMetaData,
			kQTMetaDataStorageFormatQuickTime,
			kQTMetaDataKeyFormatCommon, 
			(const UInt8 *)&key, 
			sizeof(key), 
			artwork,
			[imageData length],
			kDataTypeJPEGImage,
			NULL);
		
		OSErr updateError = UpdateMovie(movie);
		
		QTMetaDataRelease(theMetaData);
	}


-- magicmanpepe

----

I guess I jumped the gun. I did get the 'artw" attribute to save and I could varify that the 'artw' value was saved to disk with the "QTMetaData" sample code provided by ADC, but iTunes does not use this attribute when displaying album artwork. iTunes uses 'covr' and at this time QuickTime only allows you to read iTunes QTMetaData but not write these values. 

http://lists.apple.com/archives/QuickTime-API/2005/Nov/msg00032.html

Sorry to get your hopes up. --zootbobbalu


----

Since these posts Apple has added some new examples and one is covered by this tech note: http://developer.apple.com/mac/library/qa/qa2007/qa1515.html

-- Jon Steinmetz

