---
layout: page
title: RenderingSVG
---



Hi,

I am wanting to render SVG (XML) which I will have inside my application, then I'd like to take the rendered SVG file and save it out as a standard raster file such as a TIFF/PNG or similar (oh, and also have its raster implementation as an NSImage, but I guess I can always get that back from disk by loading in from the aforementioned PNG if necessary).

I know Webkit now renders SVG in Leopard, is employing Webkit just for this task the best thing to do? Is there an underlying graphics API (Quartz) that provides SVG functionality to Webkit (and which I could go and use directly rather than through Webkit)?

Any advice much appreciated, thanks for your time and thoughts!

- Nex

----

WebKit does its own SVG rendering; I'm not sure if it's the best available tool for the task (I haven't looked at what else is out there), but it should work. --DavidSmith

----

Thanks, I might take a look at it then. If anyone can suggest a relatively easy to use (on OS X from Cocoa) SVG rendering library I'd be very appreciative. I've really gone round the houses, looking at Cairo, various C libs, etc. They either have trouble installing on Leopard (via Fink or MacPorts) or they are immature/old and outdated. My MacPorts claims that I have an installed version of Cairo, but I'm at a loss as to how to use it within a Cocoa app, so if anyone has any tutorial/documentation material on that, that also would be a great help.

As an aside: I'm a little surprised at how patchy SVG functionality is, and how difficult solid, up-to-date and available libraries are to find, especially ones that are ready to go on OS X.

Thanks.

----
SVG support on Mac is patchy because we have Display PDF, which is far more powerful.  There are three things you need to support for SVG though: primitives, DOM, and JavaScript.  WebKit gets you all three; you might want to consider using it.

----

I've got a slightly dodgy way to do it for Objective-C: I use the Apache batik library (which is written in Java) and a java bridge class (remember the Java / Objective C bridge is now officially unsupported). You can see more about batik here: http://xmlgraphics.apache.org/batik/. My java bridge class is below. There is some other bits you need, like setting the classpath and an environment variable, I can dig those up for you if you email me, but I don't have them to hand right now. Also have a look at GNU libart ( http://www.levien.com/libart/ ) or maybe librsvg http://librsvg.sourceforge.net/ which may do what you need - DavidThorpe

    
//
//  SVGRasterizerBridge.java
//  SVG-Rasterizer
//
//  Created by David Thorpe on 20/01/2007.
//
package com.mutablelogic.svgrasterizer;
import java.util.Hashtable;
import java.util.Iterator;
import java.util.Map;
import java.util.StringTokenizer;
import java.util.Vector;
import java.io.File;
import org.apache.batik.apps.rasterizer.SVGConverterController;
import org.apache.batik.apps.rasterizer.SVGConverter;
import org.apache.batik.apps.rasterizer.SVGConverterSource;
import org.apache.batik.apps.rasterizer.Messages;
import org.apache.batik.apps.rasterizer.DestinationType;
import org.apache.batik.transcoder.Transcoder;

public class Bridge implements SVGConverterController {
  public void addSourceFile(String theSourceFile) {
    m_theSourceFiles.addElement(theSourceFile);
  }
  public void setDestination(String theDestination) {
    if(theDestination != null) {
      m_theDestination = new File(theDestination);
    }
  }
  public void setDestinationType(String theDestinationType) {
    if(theDestinationType=="png") {
      m_theDestinationType = DestinationType.PNG;
    } else if(theDestinationType.equals("jpg") || theDestinationType.equals("jpeg")) {
      m_theDestinationType = DestinationType.JPEG;
    } else if(theDestinationType.equals("tif") || theDestinationType.equals("tiff")) {
      m_theDestinationType = DestinationType.TIFF;
    } else if(theDestinationType.equals("pdf")) {
      m_theDestinationType = DestinationType.PDF;
    } else {
      m_theDestinationType = null;      
    }
  }

  /**
  * Scans the input vector and replaces directories with the list
   * of SVG files they contain
   */
  protected String[] expandSources(Vector sources){
    Vector expandedSources = new Vector();
    Iterator iter = sources.iterator();
    while (iter.hasNext()){
      String v = (String)iter.next();
      File f = new File(v);
      if (f.exists() && f.isDirectory()){
        File[] fl = f.listFiles(new SVGConverter.SVGFileFilter());
        for (int i=0; i<fl.length; i++){
          expandedSources.addElement(fl[i].getPath());
        }
      } else {
        expandedSources.addElement(v);
      }
    }
    
    String[] s = new String[expandedSources.size()];
    expandedSources.copyInto(s);
    return s;
  }
  
  // main execute method
  public void execute() throws java.lang.Exception {
    SVGConverter theConverter = new SVGConverter(this);
    // set options
    if(m_theDestination != null) {
      theConverter.setDst(m_theDestination);
    }
    if(m_theDestinationType != null) {
      theConverter.setDestinationType(m_theDestinationType);
    }
    String[] theSources = expandSources(m_theSourceFiles);
    theConverter.setSources(theSources);
    // perform converstion
    theConverter.execute();
  }

  // Member functions
  private Vector m_theSourceFiles = new Vector();
  private File m_theDestination = null;
  private DestinationType m_theDestinationType = null;
  
  // SVGConverterController implementation
  public static final String MESSAGE_ABOUT_TO_TRANSCODE = "Main.message.about.to.transcode"; 
  public static final String MESSAGE_ABOUT_TO_TRANSCODE_SOURCE = "Main.message.about.to.transcode.source";  
  public static final String MESSAGE_CONVERSION_FAILED = "Main.message.conversion.failed";  
  public static final String MESSAGE_CONVERSION_SUCCESS = "Main.message.conversion.success";
  
  public boolean proceedWithComputedTask(Transcoder transcoder,Map hints,Vector sources,Vector dest) {
    System.out.println(Messages.formatMessage(MESSAGE_ABOUT_TO_TRANSCODE,new Object[]{"" + sources.size()}));
    return true;
  }

  public boolean proceedWithSourceTranscoding(SVGConverterSource source,File dest) {
    System.out.println(Messages.formatMessage(MESSAGE_ABOUT_TO_TRANSCODE_SOURCE,new Object[]{source.toString(),dest.toString()}));
    return true;
  }
  
  public boolean proceedOnSourceTranscodingFailure(SVGConverterSource source,File dest,String errorCode) {
    System.out.println(Messages.formatMessage(MESSAGE_CONVERSION_FAILED,new Object[]{errorCode}));    
    return true;        
  }
  
  public void onSourceTranscodingSuccess(SVGConverterSource source,File dest) {
    System.out.println(Messages.formatMessage(MESSAGE_CONVERSION_SUCCESS,null));    
  }
}


    
/*
 *  Bridge.h
 *  SVG-Rasterizer
 *
 *  Created by David Thorpe on 20/01/2007.
 *
 */

@interface Bridge : NSObject { }

-(void)addSourceFile:(NSString* )sourceFile;
-(void)setDestination:(NSString* )destinationFile;
-(void)setDestinationType:(NSString* )destinationType;
-(void)execute;

@end


    
//
//  SVGWorker.m
//  Created by David Thorpe on 24/01/2007.
//
#import <JavaVM/JavaVM.h>
#import "Bridge.h"

@interface SVGWorker (Private)
// PROPERTIES
-(void)setBridge:(Bridge* )theBridge;
-(Bridge* )bridge;
@end

@implementation SVGWorker
-(void)dealloc {
  [m_theBridge release];
  [super dealloc];
}

-(NSString* )classpath {
  // return path to batik....
}

-(Bridge* )bridge {
  return m_theBridge;
}

-(void)setBridge:(Bridge* )theBridge {
  [theBridge retain];
  [m_theBridge release];
  m_theBridge = theBridge;
}

-(BOOL)awakeThread {
  // start java and create a bridge object
  @try {  
    // set up class path
    NSString* theClassPath = [[NSJavaVirtualMachine defaultClassPath] stringByAppendingFormat:@":%@",[self classpath]];
    [[NSJavaVirtualMachine alloc] initWithClassPath:theClassPath];
    // create a bridge object
    [self setBridge:NSJavaObjectNamedInPath(@"com.mutablelogic.svgrasterizer.Bridge", nil)];
    if([self bridge]==nil) {
      [NSException raise:@"BridgeConstructionError" format:@"Unable to create the java bridging object"];
    }
  } @catch(NSException* theException) {
    NSLog(@"Error starting JVM: %@",theException);
    return NO;
  }
  return YES;
}



----

That's fantastic, thanks, I'll take a real look at that, it's much appreciated.

I had come across Batik, it's fast, works out of the box, and appears to be a great SVG rasteriser. I just didn't want to go Java if I didn't have to (bleuch). I will look into your bridge class, that looks good, but I had been thinking that I can just call the Batik rasteriser via an NSTask and run it command-line style from within Cocoa. (I am wanting to do batch-style work so this is probably not too bad a solution).

Thanks for all your help, I think this page is now a very decent resource for info on SVG in OS X thanks to your time and input.

----

No problem, you'll also need to read up here: XcodeObjCJavaBridge, especially regarding setting the JVM version. I dug up the class path information, you'll need to add the following, assuming you're making a .jar file for your bridge class:

    
    <string>/path/to/Frameworks/SVGRasterizerBridge.jar</string>
    <string>/path/to/lib/batik-1.6/batik-rasterizer.jar</string>

