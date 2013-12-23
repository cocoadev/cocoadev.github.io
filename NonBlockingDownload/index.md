---
layout: page
title: NonBlockingDownload
---

A basic non blocking download snippet:

    
// Starts download
- (void)download
{
        NSURL *myURL = [NSURL URLWithString:@"http://www.apple.com/"];
        [myURL loadResourceDataNotifyingClient:self usingCache:YES];
}

// This method will be called when the download has finished
- (void)URLResourceDidFinishLoading:(NSURL *)sender
{
        NSData *urlContents = [sender resourceDataUsingCache:YES];

        if ([urlContents writeToFile:[@"~/Documents/applewebsite.html"
                         stringByExpandingTildeInPath]
                         atomically:YES])
        {
                // It was successful, do stuff here
        } else {
                // There was a problem writing the file
        }
}
 

EcumeDesJours

