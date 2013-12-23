---
layout: page
title: CocoaAndCarbonSaving
---

I have an application that only works in uLaw audio file formats (.au suffix). Since this is a less-than-common format on the Mac, I'm trying to add functionality to the app whereby it could accept AIFFs and output them as uLaws. I think I have the QuickTime code down, but the problem is that I'm not getting any product. I think somehow the saving isn't working right. It's made more difficult by the fact that I'd like this to be invisible; i.e., it's converted without prompting the user with the Save dialog.

    
-(IBAction)auToAIFF:(id)sender
{
	
	NSMovie * sound = [NSMovie alloc]; //This will hold the audio file.
	NSOpenPanel * pan = [NSOpenPanel openPanel]; //The open panel
if(result == NSOKButton) {
		//Get the file's path as a URL
		NSURL * url = NSURL alloc] initFileURLWithPath:[[pan filenames] objectAtIndex:0;
		
		//Print it out, just to be sure
		printf("filenames %s\n", [[pan filenames] objectAtIndex:0] description] UTF8String]);
		//Initialize the [[NSMovie with the data at the URL
		sound = [sound initWithURL:url byReference:NO];
		
		printf("QuickTimestuff\n");
		//Get the pointer to the audio file's QuickTime data
		Movie mov = [sound QTMovie];
		
		//Create a new component that will export a movie as a uLaw
		ComponentInstance ci;
		ci = OpenDefaultComponent(MovieExportType, kQTFileTypeMuLaw);
		
		//Define how to convert it. In this case, to a sample size of 16, a sample rate of 44100
		//uLawCompression with 32-bit floating point format, and 2 channels
		SoundDescriptionHandle desc;
		
		desc = (SoundDescriptionHandle )NewHandleClear(sizeof(SoundDescription));
		(**desc).descSize = sizeof(SoundDescription);
		(**desc).sampleSize = 16;
		(**desc).sampleRate = 0xAC440000;
		(**desc).dataFormat = kULawCompression | kFloat32Format;
		(**desc).numChannels = 2;
		
		//Get ready to export
		MovieExportSetSampleDescription(ci, (SampleDescriptionHandle)desc, SoundMediaType);
		
		
		//OK...now here's the saving part.
		FSSpec path;
		FSRef fsref;
		OSErr err;
		
		//CFURLRef version of the URL of the file.
		CFURLRef cfURL = (CFURLRef)url;

		//Convert the CFURLRef to an FSRef
		CFURLGetFSRef(cfURL, &fsref);
		//Convert the FSRef to an FSSpec
		err = FSGetCatalogInfo(&fsref, 
							   kFSCatInfoNone, 
							   NULL,
							   NULL,
							   &path,
							   NULL);
		//Some test lines to see what comes out
		NSString * string = [NSString stringWithCString:path.name];
		printf("%s\n", [string UTF8String]);
		
		//Do the conversion
		ConvertMovieToFile(mov, 0, &path, kQTFileTypeMuLaw, 'TVOD', smSystemScript, nil, nil, ci);
		
		//Clean up
		CloseComponent(ci);
		DisposeHandle((Handle)desc);
	}
	printf("Done\n");
}



Right now, I think it might just be saving over what it opens. The problem is, I'm not sure how to feed it a new filename to save as without using the save dialog, because as I understand it, CFURLGetFSRef(cfURL, &fsref) requires that the file already exists in order for an FSRef to exist for it. Do I need to create a new empty movie or something, then get its FSRef, then write over that?

P.S - I think my QuickTime conversion is OK, but if anyone sees a hole in it, please mention it.

----

Yes, all you're doing is saving over what it opens. There are a few funny things in your code. Why do you alloc your NSMovie, when you could do this where it's initialized (currently it's leaking memory). Your NSURL is also leaking memory. Also, do you actually run the open panel or do you just get some random filename from it (which would be rather odd). Also, if you want a URL, you don't need to create one explicitly, just call -URLs instead of -filenames. Also, you don't need to send a string a -description message -- that just returns self. 

What it seems like you should be doing is running the open panel to find the file to convert, then if you don't want any further user interaction, create a new file name with a new file extension (if applicable), maybe appending "(Converted)" to the end.

You probably don't need to get a URL at all, because you can use FSMakeFSSpec:     FSMakeFSSpec(0, 0, pascalFileName, &soundSpec);. Get pascalFileName with: 
    
CFIndex bufferSize = [newFileName length] + 1; /* + 1 for the length byte */ 
unsigned char pascalFileName[bufferSize]; 
Boolean success = CFStringGetPascalString((CFStringRef)newFileName, pascalFileName, bufferSize, CFStringGetSystemEncoding() /* or should this be kCFStringEncodingUnicode*/);
if (!success) /* raise or something */


Remember that FSSpecs can refer to non-existant files, but FSRefs cannot.

----

If that FSSpec stuff doesn't work, get an FSSpec from an FSRef, created with FSCreateFileUnicode and FSPathMakeRef (no need for URLs).

----
What I finally got to work is using the NSFileManager to create a dummy file, and a class on cocoadev.com that finds FSRefs for paths (do a search on FSSpec, it's on one of those pages).

So, here's the new code:
    
-(IBAction)conv:(id)sender
{
	NSOpenPanel * pan = [NSOpenPanel openPanel]; //The open panel
	int result = [pan runModalForDirectory:nil file:nil types:[NSSound soundUnfilteredFileTypes]];

	OSErr err;
	FSRef fileRef;
	FSSpec fsSpec;
	if(result == NSOKButton) {
		
		//getFSRefAtPath, a class I found when I ran a search on cocoadev.com for 'FSSpec'
		//Gets the FSRef for the file to convert
		err = [self getFSRefAtPath:pan filenames] objectAtIndex:0] ref:&fileRef];
		if(err != noErr)
			printf("Error in [[FSRefAtPath\n");
		//Then gets its FSSpec
		OSErr getCatalogInfoError=FSGetCatalogInfo(&fileRef, 
										   kFSCatInfoNone, 
										   NULL,
										   NULL,
										   &fsSpec,
										   NULL);
		if(getCatalogInfoError != noErr)
			printf("Error in getCatalogInfo\n");
		
		short fRefNum;
		//Initialize the QuickTime toolbox
		err = EnterMovies();
		if(err != noErr)
			printf("Error initializing toolbox\n");
		
		//Open the movie
		err = OpenMovieFile(&fsSpec, &fRefNum, fsRdPerm);
		if(err != noErr) {
			printf("Error opening movie file\n");
			printf("%d\n", err);
		}
		Movie mov;
		Boolean wasChanged;
		//Get the pointer to the Movie data. Apparently, AIFFs(which I want to convert) need this call
		err = NewMovieFromFile(&mov, fRefNum, 0, nil, 0, &wasChanged);
		if(err != noErr)
			printf("Error at NewMovieFromFile\n");
		
		FSSpec soundSpec;
		
		//For now, I'm using a static filename for testing purposes.
		NSString * newFileName = @"/Users/kennethhoffmann/Desktop/new.au";
		//Create an empty dummy file at that location
		bool hope = [[NSFileManager defaultManager] createFileAtPath:newFileName contents:nil
										 attributes:nil];
		if(hope == FALSE)
			printf("Creation failed\n");
        
		//Get its FSRef
		FSRef fileToWrite;
		err = [self getFSRefAtPath:newFileName ref:&fileToWrite];
		if(err != noErr)
			printf("Problem creating FSRef of new file %d\n", err);
		//Then its FSSpec
		getCatalogInfoError=FSGetCatalogInfo(&fileToWrite, 
												   kFSCatInfoNone, 
												   NULL,
												   NULL,
												   &soundSpec,
												   NULL);
		if(getCatalogInfoError != noErr)
			printf("Error in getCatalogInfo second time %d\n", getCatalogInfoError);
		
		//Create a new component that will export a movie as a uLaw
		printf("Conversion\n");
		
		//Create a new component that will export a movie as a uLaw
		ComponentInstance ci;
		ci = OpenDefaultComponent(MovieExportType, kQTFileTypeMuLaw);
		
		//Define how to convert it. In this case, to a sample size of 16, a sample rate of 44100
		//uLawCompression with 32-bit floating point format, and 2 channels
		SoundDescriptionHandle desc;
		
		desc = (SoundDescriptionHandle )NewHandleClear(sizeof(SoundDescription));
		(**desc).descSize = sizeof(SoundDescription);
		(**desc).sampleSize = 16;
		(**desc).sampleRate = 44100;
		(**desc).dataFormat = kULawCompression | kFloat32Format;
		(**desc).numChannels = 2;
		

		//Get ready to export
		
		MovieExportSetSampleDescription(ci, (SampleDescriptionHandle)desc, SoundMediaType);
		printf("Convert movie to File\n");
		err = ConvertMovieToFile(mov, nil, &soundSpec, kQTFileTypeMuLaw, FOUR_CHAR_CODE('TVOD'), smSystemScript, nil, 0L, ci);
		
		if(err !=noErr)
			printf("Conversion failed %d\n", err);
		else
			printf("Conversion succeeded.\n");
		CloseComponent(ci);
		DisposeHandle((Handle)desc);
        }
}



Now, a new problem and question both. The ConvertMovieToFile line is crashing on a SIGSEV 11. Honestly, I have no idea. I found most of my conversion code at http://developer.apple.com/documentation/QuickTime/RM/MusicAndAudio/Sound/index.html, but tweaked it to convert to uLaw (.au) format instead of WAVE. I'm really confused, though, if this is how I should be doing it.

There seems to be disagreement/confusion at Apple's site as to how convert audio between formats. There seem to be 3 ways.
1) ConvertMovieToFile
2) The SoundConverter API, part of Sound Manager. I thought SoundManager was deprecated, but there are examples in QuickTime's example code section using this method written as recently as 2002. It really looks painful, as you have to parse audio data into a buffer multiple times, and do all these extra calls to find the number of samples, etc.
3) CoreAudio. Too bad CoreAudio is practically undocumented.

I dunno. Maybe the best way for me to do this is to just find some UNIX tool and include it as part of the app's bundle, then run it with NSTask when I need it. I guess I feel like converting between formats shouldn't be this painful. Apple really needs to beef up NSSound. Format conversions don't seem that esoteric, but Cocoa's got nothing on them. I would be amazing if I could just say something like [NSSound convertFromFormat:(NSAudioFormat *)start toFormat:(NSAudioFormat *)finish];

----

Here a few guesses on where your problem may be: are you sure you're allowed to just typecast between SoundDescriptionHandle and SampleDescriptionHandle? I found nothing about them being equivalent in the docs. You also might want to check the result from OpenDefaultComponent(). I'm not sure where it returns errors, but there are several options: MemError() comes to mind, and ResError() might be a candidate too. It probably just returns NULL on errors, but you're not even checking against that.

Oh, and to save you a lot of work getting the UI end of importing/exporting file formats to work, you might want to read what I found out about CFBundleTypeRole today. -- UliKusterer

