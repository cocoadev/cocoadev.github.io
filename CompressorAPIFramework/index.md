---
layout: page
title: CompressorAPIFramework
---

----

This is a framework to programmatically call Apple's "Compressor" application. 
You may want to use this application if you want to use compression services
within your own Cocoa applications. You will need to install the Compressor and
Batch Monitor applications on each computer you wish to use this Framework on,
and possibly share your compression services on your network through qmaster.

I am providing this code with a BSD-style license. Please read the "LICENSE" 
file in the ZIP file for more information. You can use this code freely in your applications,
commercial and otherwise, but I would still appreciate any bugfixes or features
you add so I can roll it back into this framework and provide to everyone.

Download it here:

http://mutablelogic.com/cocoa/CompressorAPIFramework.zip (64K)


----

REQUIREMENTS

This framework is tested on

 
  * Macintosh G5 PowerPC
  * Mac OS X Tiger 10.4.7
  * Compressor 2.1
 

You'll need a local copy of Final Cut Studio. You mileage may vary on other platforms and versions.

----
USING THE FRAMEWORK

You should create an instance of the "Compressor" class, passing thorugh the 
locations of the Compressor and Batch Monitor command-line executables:

    
  NSString* theCompressorExecutable = 
    @"/Applications/Compressor.app/Contents/MacOS/Compressor";
  NSString* theBatchMonitorExecutable = 
    @"/Applications/Utilities/Batch Monitor.app/Contents/MacOS/Batch Monitor";

  Compressor* theCompressor = 
    [Compressor compressorWithExecutable:theCompressorExecutable 
              withBatchMonitorExecutable:theBatchMonitorExecutable]];


----
COMPRESSOR CLUSTERS

The Compressor instance will discover compression clusters in a background 
thread. You can at any time retrieve the current set of clusters as an array
of dictionary items (later, I may add a new class called CompressorCluster):

    
  NSArray* theClusters = [theCompressor clusters];
 

Initially there will be no clusters and it may take a few seconds for any 
existing clusters to be discovered. See the section on "Delegates" below to
determine when clusters are discovered.

----
COMPRESSOR SETTINGS

You can create or use Compressor presets within the Apple Compressor 
application. Custom ones (and soft links to the Apple-provided presets) are 
stored in your ~/Library/Application Support/Compressor folder. Each has a file
extension of ".setting". To submit jobs to Compressor, you will need to register
a preset file with the Compressor class:

    
  CompressorSetting* thePreset = [Compressor settingFromFile:thePresetFile];


----
SUBMITTING A COMPRESSOR JOB

To submit a compressor job, you will need to know:

 
  * The source media file 
  * The desintation path
  * The cluster address (or cluster ID)
  * The compressor setting you wish to use
 

Here is how a job is submitted programmatically:

    
  NSError* theError = nil;  
  CompressorJob* theJob =
    [theCompressor submitJobToCluster:theClusterAddress 
                            withMedia:theFilename 
                          withSetting:thePreset 
                    toDestinationPath:thePath 
                                error:&theError];
  if(theError) {
     NSLog(@"Error submitting job = %@",theError);
  }


Submitting a job can take a little while so if you have a UI to control you may
want to submit the job in the background, not in the main thread.

----
COMPRESSOR JOBS

You can then monitor the compressor job using the following methods of 
CompressorJob:

    
  -(NSDate* )submissionTime;
  -(NSString* )sentBy;
  -(NSString* )jobType;
  -(NSString* )priority;
  -(NSString* )timeElapsed;
  -(NSString* )timeRemaining;
  -(float)percentComplete;
  -(NSString* )status;


You can also get an array of current jobs:

    
  NSArray* theJobs = [theCompressor jobs];
 
 
A delegate message tells you information about when jobs are started, 
terminated and updated.

----
COMPRESSOR DELEGATES

The Compressor instance will send messages to a delegate. The messages that are 
sent to the controller as the delegate are as follows:

 

* <code>-(void)clustersDidChange:(id)sender;</code>
    This message is sent to the delegate when compressor cluster discovery has
    completed. At the moment, this is NOT sent in the main thread, which could
    be a problem for you. 
   
* <code>-(void)jobDidStart:(id)sender;</code>
    This message is sent when a compressor job is starting to process. The 
    'sender' argument is the CompressorJob instance which has changed.
    
* <code>-(void)jobDidUpdate:(id)sender;</code>
    This message is sent when a compressor job has updated. The 
    'sender' argument is the CompressorJob instance which has changed.

* <code>-(void)jobDidTerminate:(id)sender;</code>
    This message is sent when a compressor job has terminated. The 
    'sender' argument is the CompressorJob instance which has changed.



----
EXAMPLE CODE

The XCode project which you can download from the link above contains an example Cocoa application which is like a 
mini-"Compressor" application without the fancy Apple stuff.


-DavidThorpe, 7th September 2006

