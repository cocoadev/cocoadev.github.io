---
layout: page
title: IPhotoExporter
---

The iPhotoExporter plugin protocols dumped from class-dump can be quite confusing.
Here's some of the things I've uncovered while building exporter .

(how to get started building a plugin: http://www.stone.com/The_Cocoa_Files/Writing_PlugIns.html )

    

ExportPluginProtocol methods

/* This method is called by the exportManager to update the export status sheet.
 * Allocate an exportProgress struct (see below) and supply the proper values to 
 * update the status sheet.
 *(?) I'm assuming the calling method cleans up your allocated struct, freeing it
 * yourself causes a fault.
 */
- (exportProgress*)progress;

/* Passing YES in your exportProgress from -progress; causes the exportManager to
 * subsequently call this method.
 */
- (void)lockProgress;

/* This method is called when the user clicks "Export" from the export settings pane.
 * Here is a good place to call -[exportManager startExport] to drop the export
 * status sheet.
 */
- (void)startExport:(id)fp12; 

/* This is called after the export status sheet is dropped.
 */
- (void)performExport:(id)fp12;

/*  Called by clicking "cancel" on the export status sheet
 */
- (void)cancelExport; 

Anonymous Structs (add these to your ExportPluginProtocol.h)

typedef struct {
    unsigned long progressCount;	//progressbar current
    unsigned long progressTotal;	//progressbar total
    NSString* progressString;		//progress message
    BOOL indeterminant;			//progressbar type
//locks the progress; will cause -lockProgress; (I don't know what this is supposed to do)
    BOOL lockProgress;		
    BOOL isDone;			//YES will close the window
} exportProgress;




--- KelvinNishikawa --

