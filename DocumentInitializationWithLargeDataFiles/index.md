---
layout: page
title: DocumentInitializationWithLargeDataFiles
---

I have just started Cocoa development using XCode 2.1. My application is to load big binary files (signals from a scientific research tool) and process the data and eventually show some images. I'm currently using Cocoa document based setting with Objective-C.  I am observing quite a mysterious phenomenon.

I created myDocument class derived from NSDocument. It mallocs a bunch of SInt16, UInt32, and float arrays necessary for the processing. All of them are freed after processing. The end results are displayed as an instance of myImage class that derived from CIImage (I'm planning to use core image filtering later on). In Debug mode, my code works perfectly for the first time only. Whenever I open 2nd or 3rd files, the output looks weird as if it reads totally different location of the memory. In my understanding, with the document based application, one file is assigned to one instance of myDocument class. And any new files should be assigned to newly allocated instances so that there should not be any memory overlapping. Is my assumption correct? 

Furthermore, when I launch from MallocDebug, my code works perfectly!!! I can open as many files as I want without having any problem. Why is that?

----

Please PostYourCode.

----

    
myDocument class
- (BOOL)readFromFile:(NSString *)fileName ofType:(NSString *)docType
{
    BOOL openSuccess = NO;
    NSString *extension = fileName pathExtension] lowercaseString];
    if ([extension isEqualToString:@"bin"]) {
        // Bin file
        sourceFile = [[fileName copy] autorelease];
        documentType = [[docType copy] autorelease];
        [self prepareGlobalArray];
        [self processBinaryData];
        openSuccess = YES;
    }
    if (openSuccess)
        [[NSLog([@"Opening a file: " stringByAppendingString: docType]);
    return openSuccess;
}

- (void)prepareGlobalArray
{
    globalArray = malloc((int)sampling*sizeof(float));
    cLUT = malloc(256*3*sizeof(UInt8)); // UInt8 for color look up table

    // Prepare globalArray and cLUT
}

- (void)processBinaryData
{
    NSFileHandle *myData = [NSFileHandle fileHandleForReadingAtPath:sourceFile];
    SInt16 *buffer = calloc(pixelsY, sizeof(SInt16));
    float *fftBuffer = calloc(pixelsY, sizeof(float));
    UInt32 *rawData = calloc(pixelsX*pixelsY/2, sizeof(UInt32));
    
    // Prepare vDSP FFT
    COMPLEX_SPLIT A;
    FFTSetup setupReal;
    UInt32 log2n;
    UInt32 n, nOver2;
    SInt32 stride;
    
    // Set the size of FFT.
    log2n = 11; // 2048 samplings
    n = 1 << log2n;
    
    stride = 1;
    nOver2 = n / 2;
    
    // Allocate memory for the input operands and check its availability,
    // use the vector version to get 16-byte alignment.
    A.realp = calloc ( nOver2, sizeof ( float ) );
    A.imagp = calloc ( nOver2, sizeof ( float ) );
    
    // Set up the required memory for the FFT routines and check its availability.
    setupReal = vDSP_create_fftsetup ( log2n, FFT_RADIX2);
    
    // Read and process data
    for (x = 0; x < pixelsX; x++) {
        myData readDataOfLength: sizeOfAscan] getBytes: buffer];
        // Do some processing...
        
        // Look at the real signal as an interleaved complex vector by
        // casting it.
        // Then call the transformation function vDSP_ctoz to get a split
        // complex vector,
        // which for a real signal, divides into an even-odd configuration.
        vDSP_ctoz ( ( COMPLEX * ) fftBuffer, 2, &A, 1, nOver2 );
		
        // Carry out a Forward FFT transform.
        vDSP_fft_zrip ( setupReal, &A, stride, log2n, FFT_FORWARD );
        		
        for (y = 0; y < pixelsY; y++) 
            rawData[index+y] = (UInt32)(abs(logf(A.realp[y]*A.realp[y]+A.imagp[y]*A.imagp[y]))*10.0);
    }
    [myData closeFile];
    
    // Generate image after getting min and max within rawData...
    UInt8 *imageData = malloc(pixelsX*pixelsY/2*sizeof(UInt8));
    float factor = 255.0/((float)max-(float)min);
    for (x = 0; x < pixelsX*pixelsY; x++) {
        data = ((float)rawData[x]-(float)min)*factor;
        if (data < 0)
            data = 0.0;
        else if (data > 255.0)
            data = 255.0;
        imageData[x] = (UInt8)data;
    }
    
    // Generate [[CIImage
    unsigned char *bitmapData = calloc(pixelsX*pixelsY*4, sizeof(unsigned char));
    int crIndex;
    for (y = 0; y < pixelsY; y++)
        for (x = 0; x < pixelsX; x++) {
            index = y*pixelsX*4+x*4;
            bitmapData[index] = 255; // Alpha level is always 255
            crIndex = imageData[x*pixelsY+y]*3;
            
            // False color version
            bitmapData[index+1] = cLUT[crIndex];  // cLUT: color look up table (UInt8 array) in RGB value (0-255)
            bitmapData[index+2] = cLUT[crIndex+1];
            bitmapData[index+3] = cLUT[crIndex+2];  
        }
    finalData = [[[NSData alloc] initWithBytes:bitmapData
                               length:pixelsX*pixelsY*4] autorelease];
    currentImage = [[CIImage alloc] initWithBitmapData:finalData
                                     bytesPerRow:pixelsX*4
                                            size:CGSizeMake((float)pixelsX, (float)pixelsY/2.0)
                                          format:kCIFormatARGB8
                                            colorSpace:CGColorSpaceCreateDeviceRGB()];
    
    // Free the allocated memory.
    vDSP_destroy_fftsetup ( setupReal );
    free ( A.realp );
    free ( A.imagp );	
    
    free(buffer);
    free(fftBuffer);
    free(bitmapData);
    free(imageData);
    free(rawData);
    free(cLUT);
    free(globalArray);
}


currentImage is referred from a subclass of NSWindowController and passed it to a subclass of NSView so that the associated window can show the generated image.

Once again, whenever I launch this application from MallocDebug, it works fine and I can open as many files as I want without any problem.

But if I simply run the executable without using MallocDebug, the first file is displayed properly.  But from the 2nd file, it looks like as if the software is reading totally different memory space...

----

Maybe you are trying to do too much at once. Why don't you try letting the readFromFile do its one job and then
trigger the prepareGlobalArray and processBinaryData with another control?

*Or, for a novel idea, use the debugger. ;-) Set a breakpoint in the beginning of the readFromFile method and step through it as it opens the first, then again as it opens the next. You'd be surprised what the debugger can tell you.*

----

It doesn't exactly help much, but I've removed the tabs from your source code and swapped mallocs for (more correct?) callocs.

I'm a little concerned by the line     rawData[index+y] = (UInt32)(abs(logf(A.realp[y]*A.realp[y]+..., since     index doesn't appear to be initialized (or, indeed, declared). Did you mean     x*pixelsY here perhaps? That'd cause the problem for sure.

Try running lint over your code, and turning all warnings on (-Wall -Wpedantic is it?).

----

Thanks for your input.  I accidentally erased the line initializing "index" when I copied the code.  Yes, it is properly initialized.

As an interlim solution, I will create a new class just to handle binary files and try to create an instance of this class after myDocument completes readFromFile function.  I will report the result tomorrow.

*Fix the code above, perhaps? We can't help you any further if we know the code isn't correct :)*

----

OK, I separated the data processing from myDocument class.  Now myDocument is simply initialize myWindow by passing the file path to be processed.  After myWindow awakeFromNib, it initializes myData with the file path.  All the processing is done in myData and CIImage object is passed to myView on myWindow.  Now everything is working beautifully.

So what I found was that NSDocument class is initialized for each TYPE of document and is not necessarily spawned for each DOCUMENT users open.  On the other hand, NSWindow class is initialized every time NSDocument opens a file.  This is why my initial code did not work after first file was opened.  Strange thing is that MallocDebug seems to force NSDocument to spawn new instance every time a file opened.  I still wonder why...

Mm, doesn't sound right.  Each document is indeed a different object.

Yeah, I won't dispute the fact that you fixed it, but your reasons are utterly bogus.

----

*So what I found was that NSDocument class is initialized for each TYPE of document and is not necessarily spawned for each DOCUMENT users open. *

This is untrue. A new instance of your NSDocument class is instantiated for every document in your application.

*Well, not exactly. An instance of NSDocument *can* be reinitialized (in the English sense, not the Cocoa sense) by reloading the data from a     read method (like     readFromFile:ofType:). --JediKnil**

Having said that, I don't know what your problem is, and I won't know until I see your entire project, along with your source data. 

It's completely appropriate to do this kind of model crunching in your NSDocument subclass if that class is the entirety of your model data. In this case, it looks like that is true. In larger applications, NSDocument is merely a controller class that routes data between model objects and view objects. In your case a model object would be a new Objective-C class that is responsible for decyphering your binary data and converting it to different forms. C.f., NSImage for an example. Doing number-cruncing in view objects, such as your NSWindow instance, is a terrible practice and will limit the reusability of your code.

Enjoy! -- MikeTrent

----

I see something strange in the lines

            sourceFile = fileName copy] autorelease];

            documentType = [[docType copy] autorelease];

I wonder about their use in what follows, vis-a-vis the [[NSFileHandle myData.

