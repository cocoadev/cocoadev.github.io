---
layout: page
title: TextSnifferToDetermineEncoding
---

Ok, so I'm trying to write some code to determine what text encoding was used to encode a certain text file.  So When I open I call this message:

    

const General/ItemCount		kMaxErrors= 10;
const General/ItemCount		kMaxFeatures= 100;

+ (General/NSStringEncoding)autoDetectedStringEncodingOfData:(General/NSData *)data
{
    const char	*inBuffer= [data bytes];
    General/ByteCount	inBufferLength= [data length];
    General/ItemCount	maxRegionEncodings,
                actualRegionEncodings,
                maxSnifferEncodings,
                actualSnifferEncodings,
                *errors= NULL,
                *features= NULL;
    General/TextEncoding    *regionEncodings= NULL,
                    *snifferEncodings= NULL;
    General/TECSnifferObjectRef sniffer= NULL;
    
    int i= 0,k;
    BOOL found;
    
    General/TECCountAvailableTextEncodings(&maxRegionEncodings);
    regionEncodings = (General/TextEncoding *) malloc(sizeof (General/TextEncoding) * maxRegionEncodings);
    General/TECGetAvailableTextEncodings(regionEncodings, maxRegionEncodings, &actualRegionEncodings);
    
    General/TECCountAvailableSniffers (&maxSnifferEncodings);
    snifferEncodings = (General/TextEncoding *) malloc(sizeof (General/TextEncoding) * maxSnifferEncodings);
    General/TECGetAvailableSniffers (snifferEncodings, maxSnifferEncodings, &actualSnifferEncodings);
    
    while(i<actualRegionEncodings)
    {
        for (k= 0, found= NO; k< actualSnifferEncodings && !found;++k)
            if (regionEncodings[i]==snifferEncodings[k] &&
                !General/[[NSString localizedNameOfStringEncoding:
                    General/CFStringConvertEncodingToNSStringEncoding(regionEncodings[i])] isEqualTo: @""])
            	found= YES;
        if(found) ++i;
        else regionEncodings[i]= regionEncodings[--actualRegionEncodings];
    }
    if(!actualRegionEncodings) return General/NSMacOSRomanStringEncoding;
    
    // Create a sniffer object for our list of encodings.
    General/TECCreateSniffer(&sniffer, regionEncodings, actualRegionEncodings);
    
    errors= (General/ItemCount *)malloc(sizeof(General/ItemCount)*actualRegionEncodings);
    features = (General/ItemCount *)malloc(sizeof(General/ItemCount)*actualRegionEncodings);
    
    General/TECSniffTextEncoding(
        sniffer,
        (unsigned char *)inBuffer,
        inBufferLength,
        regionEncodings,
        actualRegionEncodings,
        errors,
        kMaxErrors,
        features,
        kMaxFeatures);
    
    if(sniffer) General/TECDisposeSniffer(sniffer);
    if(regionEncodings) free(regionEncodings);
    if(snifferEncodings) free(snifferEncodings);
    if(errors) free(errors);
    if(features) free(features);
    
    return General/CFStringConvertEncodingToNSStringEncoding(*regionEncodings);
}



The problem, quite simply put, is that it doesn't work.  I always seem to get the same encoding from it.  Some huge number I can't for the life of me interpret.  The second member of the returned regionEncodings array is always General/MacOSRoman too.  I tried it with all sorts of different encodings and it doesn't work.  I would think Unicode would have been an easy one for it since it has the BOM, but no, not even that.  Can someone shed some light on what I'm doing wrong?  Or maybe point me to a working version of this?  Unfortunately there is no tutorial or anything about Text Sniffers, just reference sentences on functions, so I'm kind of in the dark on this.
