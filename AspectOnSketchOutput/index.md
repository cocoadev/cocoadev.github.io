---
layout: page
title: AspectOnSketchOutput
---

AspectOnSketchOutput - part of AspectCocoa

the following is the output of AspectOnSketch, the result of an ACFileLoggingAdvice as the AdviceObject to an LookupAspect on the program Sketch with a PointCut specifying all classes not in Foundation/Application/AspectCocoa
 
    
  before init on SKTDrawDocument 
  after init on SKTDrawDocument 
  before makeWindowControllers on SKTDrawDocument 
       before init on SKTDrawWindowController 
       after init on SKTDrawWindowController 
       before setDocument: on SKTDrawWindowController 
            before setUpGraphicView on SKTDrawWindowController 
                 before documentSize on SKTDrawWindowController 
                      before documentSize on SKTDrawDocument 
                      after documentSize on SKTDrawDocument 
                 after documentSize on SKTDrawWindowController 
            after setUpGraphicView on SKTDrawWindowController 
       after setDocument: on SKTDrawWindowController 
  after makeWindowControllers on SKTDrawDocument 
  before initWithFrame: on SKTGraphicView 
  after initWithFrame: on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before windowDidLoad on SKTDrawWindowController 
       before setDrawWindowController: on SKTGraphicView 
       after setDrawWindowController: on SKTGraphicView 
       before setUpGraphicView on SKTDrawWindowController 
            before documentSize on SKTDrawWindowController 
                 before documentSize on SKTDrawDocument 
                 after documentSize on SKTDrawDocument 
            after documentSize on SKTDrawWindowController 
       after setUpGraphicView on SKTDrawWindowController 
       before becomeFirstResponder on SKTGraphicView 
            before updateRulers on SKTGraphicView 
            after updateRulers on SKTGraphicView 
       after becomeFirstResponder on SKTGraphicView 
       before selectedToolDidChange: on SKTDrawWindowController 
            before init on SKTToolPaletteController 
            after init on SKTToolPaletteController 
            before currentGraphicClass on SKTToolPaletteController 
            after currentGraphicClass on SKTToolPaletteController 
       after selectedToolDidChange: on SKTDrawWindowController 
  after windowDidLoad on SKTDrawWindowController 
  before acceptsFirstResponder on SKTGraphicView 
  after acceptsFirstResponder on SKTGraphicView 
  before acceptsFirstResponder on SKTGraphicView 
  after acceptsFirstResponder on SKTGraphicView 
  before acceptsFirstResponder on SKTGraphicView 
  after acceptsFirstResponder on SKTGraphicView 
  before acceptsFirstResponder on SKTGraphicView 
  after acceptsFirstResponder on SKTGraphicView 
  before acceptsFirstResponder on SKTGraphicView 
  after acceptsFirstResponder on SKTGraphicView 
  before acceptsFirstResponder on SKTGraphicView 
  after acceptsFirstResponder on SKTGraphicView 
  before isOpaque on SKTGraphicView 
  after isOpaque on SKTGraphicView 
  before isOpaque on SKTGraphicView 
  after isOpaque on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before drawRect: on SKTGraphicView 
       before drawWindowController on SKTGraphicView 
       after drawWindowController on SKTGraphicView 
       before showsGrid on SKTGraphicView 
       after showsGrid on SKTGraphicView 
       before graphics on SKTDrawDocument 
       after graphics on SKTDrawDocument 
  after drawRect: on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before applicationDidFinishLaunching: on SKTDrawAppDelegate 
       before showToolPaletteAction: on SKTDrawAppDelegate 
            before windowDidLoad on SKTToolPaletteController 
            after windowDidLoad on SKTToolPaletteController 
       after showToolPaletteAction: on SKTDrawAppDelegate 
  after applicationDidFinishLaunching: on SKTDrawAppDelegate 
  before isOpaque on SKTGraphicView 
  after isOpaque on SKTGraphicView 
  before isOpaque on SKTGraphicView 
  after isOpaque on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before drawRect: on SKTGraphicView 
       before drawWindowController on SKTGraphicView 
       after drawWindowController on SKTGraphicView 
       before showsGrid on SKTGraphicView 
       after showsGrid on SKTGraphicView 
       before graphics on SKTDrawDocument 
       after graphics on SKTDrawDocument 
  after drawRect: on SKTGraphicView 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before selectToolAction: on SKTToolPaletteController 
       before selectedToolDidChange: on SKTDrawWindowController 
            before currentGraphicClass on SKTToolPaletteController 
            after currentGraphicClass on SKTToolPaletteController 
       after selectedToolDidChange: on SKTDrawWindowController 
  after selectToolAction: on SKTToolPaletteController 
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before mouseDown: on SKTGraphicView 
       before currentGraphicClass on SKTToolPaletteController 
       after currentGraphicClass on SKTToolPaletteController 
       before editingGraphic on SKTGraphicView 
       after editingGraphic on SKTGraphicView 
       before clearSelection on SKTGraphicView 
       after clearSelection on SKTGraphicView 
       before createGraphicOfClass:withEvent: on SKTGraphicView 
            before drawDocument on SKTGraphicView 
                 before drawWindowController on SKTGraphicView 
                 after drawWindowController on SKTGraphicView 
            after drawDocument on SKTGraphicView 
            before init on SKTRectangle 
                 before setBounds: on SKTRectangle 
                      before didChange on SKTRectangle 
                      after didChange on SKTRectangle 
                      before undoManager on SKTRectangle 
                           before document on SKTRectangle 
                           after document on SKTRectangle 
                      after undoManager on SKTRectangle 
                      before didChange on SKTRectangle 
                      after didChange on SKTRectangle



the output continues far beyond the max chars allowed by this wiki, but here's a snippet of when it does not crash on dealloc

    
                                               after updateRulers on SKTGraphicView 
                                          after deselectGraphic: on SKTGraphicView 
                                     after invalidateGraphic: on SKTGraphicView 
                                after invalidateGraphic: on SKTDrawWindowController 
                           after invalidateGraphic: on SKTDrawDocument 
                      after removeGraphicAtIndex: on SKTDrawDocument 
                 after removeGraphic: on SKTDrawDocument 
                 before dealloc on SKTRectangle 
                 after dealloc on FREED(id) 
                 before drawDocument on SKTGraphicView 
                      before drawWindowController on SKTGraphicView 
                      after drawWindowController on SKTGraphicView 
                 after drawDocument on SKTGraphicView 
            after delete: on SKTGraphicView 


here's a snippet of when it does crash on dealloc

    
  before isFlipped on SKTGraphicView 
  after isFlipped on SKTGraphicView 
  before dealloc on SKTDrawDocument 
       before dealloc on SKTLine 
       after dealloc on (null) 
       before dealloc on SKTCircle 
       after dealloc on ontentRect:styleMask:backing:defer:

