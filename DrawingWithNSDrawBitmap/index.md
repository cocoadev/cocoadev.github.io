---
layout: page
title: DrawingWithNSDrawBitmap
---



Does anyone know to write to an NSGraphicsContext with NSDrawBitmap, or using the NSBitmapImageRep? All I need is the ability to write color data to a given pixel. I know you can draw with NSRectFillListWithColors(), but it's painfully slow. I'm trying to figure out how to do simple pixel-level image manipulation.

----

    

typedef struct _BIRPixel {
    unsigned char red;
    unsigned char green;
    unsigned char blue;
    unsigned char alpha;
} BIRPixel;


void birTest() {
    int width=100;
    int height=100;
    id bir=[[[NSBitmapImageRep alloc] initWithBitmapDataPlanes:nil
                pixelsWide:width
                pixelsHigh:height
                bitsPerSample:8
                samplesPerPixel:4
                hasAlpha:YES
                isPlanar:NO
                colorSpaceName:NSDeviceRGBColorSpace
                bytesPerRow:width*4
                bitsPerPixel:8*4] autorelease];
    BIRPixel *pixels=(BIRPixel *)[bir bitmapData];
    char *bytes=(char *)pixels;
    memset(bytes, 255, width*height*4);  // paints all pixels white
    int row, column;
    int rowOffset;
    // top left corner is painted red
    for (row=0;row<50;row++) {
        rowOffset=row*width;
        for (column=0;column<50;column++) {
            pixels[rowOffset+column].red=255;
            pixels[rowOffset+column].green=0;
            pixels[rowOffset+column].blue=0;
            pixels[rowOffset+column].alpha=255;
        }
    }
    // top right corner is painted green
    for (row=0;row<50;row++) {
        rowOffset=row*width;
        for (column=50;column<100;column++) {
            pixels[rowOffset+column].red=0;
            pixels[rowOffset+column].green=255;
            pixels[rowOffset+column].blue=0;
            pixels[rowOffset+column].alpha=255;
        }
    }
    // bottom left corner is painted blue
    for (row=50;row<100;row++) {
        rowOffset=row*width;
        for (column=0;column<50;column++) {
            pixels[rowOffset+column].red=0;
            pixels[rowOffset+column].green=0;
            pixels[rowOffset+column].blue=255;
            pixels[rowOffset+column].alpha=255;
        }
    }
    // bottom right corner is painted purple
    for (row=50;row<100;row++) {
        rowOffset=row*width;
        for (column=50;column<100;column++) {
            pixels[rowOffset+column].red=255;
            pixels[rowOffset+column].green=0;
            pixels[rowOffset+column].blue=255;
            pixels[rowOffset+column].alpha=255;
        }
    }
    
    
    id tiff=[bir TIFFRepresentation];
    [tiff writeToFile:@"/Users/zoot/temp/bir.tiff" atomically:YES];
}




If you are familiar with C, then you should know how to manipulate bytes without using a struct. But, if you are not familiar with C then I would play with this struct (BIRPixel) until you get a feel for how the byte order works. Basically the sequence of bytes goes RGBARGBARGBA for Red-Green-Blue-Alpha-Red-Green-Blue-Alpha-Red-Green-Blue-Alpha. If you notice, the order of the members of the struct BIRPixel are also Red-Green-Blue-Alpha. Once you get the hang of this byte order you can use a pointer of type (unsigned char *) to manipulate the individual color components. 

    
int r,c;
unsigned char redValue, greenValue, blueValue, alphaValue;
int rowOffset;
unsigned char *bytes=[bir bitmapData];

for (r=0;r<height;r++) {
    rowOffset=r*width*4;
    for (c=0;c<width;c++) {
        *bytes++=redValue;
        *bytes++=greenValue;
        *bytes++=blueValue;
        *bytes++=alphaValue;
    }
}




The bitmap data is rastered in a Z pattern starting with the top row going from left to right and proceeding down each row until the bottom row.

--zootbobbalu  

WhoOwnsNSBitmapImageRepBuffers

