---
layout: page
title: DiscID
---

I'm going to help a friend rip his entire CD collection (300+) into mp3 file format. The only problem is he has dialup and and a single shared phone line. I have already downloaded freeDB's database (really large if you leave all entries in uncompressed individual files), so most of the track info will be on a local HD. My only problem is getting cd-discid or discid to work on Panther. I only need the 32 bit hex id to access the database. Has anyone gotten either of these command line tools to work in Panther? Does anyone know of a way to get iTunes to reveal the "disc id" of a mounted CD?

BTW, when I try to use cd-discid by passing "/dev/disk2" as the only argument I get the following error:

    cd-discid: /dev/disk2: DKIOCCDREADTRACKINFO: Input/output error

----

ok, here's what I figured out. Just give     UmDiscIDAtPath the path to the table of contents plist found in the directory of the mounted audio cd volume (the file is named     .TOC.plist). Hopefully OS X will always provide this property list. Now all I have to do is parse 1 GB of cddb entries!! --zootbobbalu

    
extern unsigned int UmDiscIDAtPath(NSString *path); 

static int UmCDDBSum(int n)
{
    int ret = 0;
    while (n > 0) {ret += (n % 10); n /= 10;}
    return (ret);
}

unsigned int UmDiscIDAtPath(NSString *path) {

    if (!path) return nil;
    NSDictionary *plist = [NSDictionary dictionaryWithContentsOfFile:path];
    NSArray *sessions = [plist objectForKey:@"Sessions"];
    int i, sum = 0, leadInBlock, cnt;
    if ([sessions count]) {
        NSDictionary *session1 = [sessions objectAtIndex:0];
        NSArray *tracks = [session1 objectForKey:@"Track Array"];
        cnt = [tracks count];
        for (i = 0; i < cnt; i++) {
            int block = [tracks objectAtIndex:i] objectForKey:@"Start Block"] intValue];
            sum += [[UmCDDBSum(block / 75);
            if (!i) leadInBlock = block;
        }
        return ((sum % 0xff) << 24 | 
                (session1 objectForKey:@"Leadout Block"] intValue] - leadInBlock) / 75 << 8 | cnt);
    }
    return nil;
}



----

I just started looking at [[MusicBrainz ( see http://www.musicbrainz.org/ ) to get CD metadata, links to Amazon, and so forth.
The starting point is to generate a discid from the .TOC.plist file, which is read when you insert the CD. Here's my version:

    

-(NSString* )TOCPath {
   // TODO: return the path to the .TOC.plist file from this method
}

-(NSDictionary* )TOC {
  return [NSDictionary dictionaryWithContentsOfFile:[self TOCPath]];
}

-(NSDictionary* )sessionWithIndex:(unsigned)theSession {
  NSArray* theArray = self TOC] objectForKey:@"Sessions"];
  if([theArray isKindOfClass:[[[NSArray class]]==NO) return nil;
  if(theSession >= [theArray count]) return nil;
  return [theArray objectAtIndex:theSession];
}

-(NSString* )discID {
  // see: http://wiki.musicbrainz.org/DiscIDCalculation
  // always use the first session for this - may not be right thing to do???
  NSDictionary* theSession = [self sessionWithIndex:0]; 

  // write header
  NSMutableString* theString = [NSMutableString stringWithFormat:@"%02X%02X%08X",
    theSession objectForKey:@"First Track"] unsignedIntValue],
    [[theSession objectForKey:@"Last Track"] unsignedIntValue],
    [[theSession objectForKey:@"Leadout Block"] unsignedIntValue;

  // write tracks
  NSArray* theTracks = [theSession objectForKey:@"Track Array"];
  for(unsigned theTrack = 0; theTrack < 99; theTrack++) {
    if(theTrack < [theTracks count]) {
      [theString appendFormat:@"%08X",[theTracks objectAtIndex:theTrack] objectForKey:@"Start Block"] unsignedIntValue;
    } else {
      [theString appendString:@"00000000"];
    }
  }
  
  // compute SHA1 digest
  NSData* theDigest = theString dataUsingEncoding:NSUTF8StringEncoding] sha1Digest];
  // convert to base 64
  [[NSMutableString* theBase64 = [NSMutableString stringWithString:[theDigest encodeBase64WithNewlines:NO]];
  // to convert to MusicBrainz format, convert some non-URI characters...
  // replace '=' with '-', '+' with '.' and '/' with '_'
  [theBase64 replaceOccurrencesOfString:@"=" withString:@"-" options:0 range:NSMakeRange(0,[theBase64 length])];
  [theBase64 replaceOccurrencesOfString:@"+" withString:@"." options:0 range:NSMakeRange(0,[theBase64 length])];
  [theBase64 replaceOccurrencesOfString:@"/" withString:@"_" options:0 range:NSMakeRange(0,[theBase64 length])];
  // return the discid
  return theBase64;
}



You'll need the NSData additions from the BaseSixtyFour page on this wiki.

- DavidThorpe

