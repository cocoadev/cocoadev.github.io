---
layout: page
title: SketchDotAppUndo
---

SketchDotAppUndo

I am having trouble figuring out how parts of the example application Sketch.app work with undo.

For instance:

the method on SKTGraphicView defines an action name for an undo operation, but does not actually call prepareWithInvocationTarget: or a registerUndoWithTarget: etc... or anything I can find to define how to undo a delete...
How is the undo functionality implemented in this case?  How does the UndoManager know how to undo the delete: ? Am I missing some code somewhere?
    

- (IBAction)delete:(id)sender {
    NSArray *selCopy = [[NSArray allocWithZone:[self zone]] initWithArray:[self selectedGraphics]];
    if ([selCopy count] > 0) {
        self drawDocument] performSelector:@selector(removeGraphic:) withEachObjectInArray:selCopy];
        [selCopy release];
        [[[self drawDocument] undoManager] setActionName:
            [[NSLocalizedStringFromTable(@"Delete", @"UndoStrings", @"Action name for deletions.")];
    }
}



----

Basically,

    self drawDocument] performSelector:@selector(removeGraphic:) withEachObjectInArray:selCopy]

Will call     removeGraphic: for each selected graphic:

    
// in [[SKTDrawDocument.m
- (void)removeGraphic:(SKTGraphic *)graphic {
    unsigned index = [_graphics indexOfObjectIdenticalTo:graphic];
    if (index != NSNotFound) {
        [self removeGraphicAtIndex:index];
    }
}


Which calls this method:

    
// in SKTDrawDocument.m
- (void)removeGraphicAtIndex:(unsigned)index {
    id graphic = _graphics objectAtIndex:index] retain];
    [_graphics removeObjectAtIndex:index];
    [self invalidateGraphic:graphic];
    [[[self undoManager] prepareWithInvocationTarget:self] insertGraphic:graphic atIndex:index];
    [graphic release];
}


And that's where the     prepareWithInvocationTarget: method is getting called. -- [[RyanBates

----

Using AspectCocoa to generate a trace of what's going on really helps a lot in this case, SketchDotAppUndoTrace

The real problem here isn't figuring out how Sketch.app works with undo, it is designing an application that is well structured.  Clearly in this case, it is confusing to have the undo functionality structured in this way.  This is exactly the sort of problem that Aspect-Oriented Programming was created to solve.  By defining undo functionality in a seperate aspect, not mixed in with the rest of the program, we might be able to figure out what's going on by reading the code and not having to trace the method calls.  This is known as a seperation of concerns. -JacobBurkhart

*Just my two cents, I don't think separating the undo functionality from the rest of the program will help solve this issue. Part of the beauty of the undo manager is the almost "free" redo support which wouldn't be possible if it were separated. As far as the Sketch example goes, I do consider it confusing that the SKTGraphic class contains the removeGraphic: method (why would an object be removing itself?), but then again, I haven't studied the Sketch example well enough to fully understand the reasoning behind this. -- RyanBates*

actually it's in SKTDrawDocument not SKTGraphic... code above reflects this correction
    
2004-04-07 15:01:26.492 Sketch[394] ---after drawDocument on SKTGraphicView
2004-04-07 15:01:26.492 Sketch[394] ---before removeGraphic: on SKTDrawDocument
2004-04-07 15:01:26.492 Sketch[394] ----before removeGraphicAtIndex: on SKTDrawDocument

although it appears you posted the original code, so maybe we're working with different versions of Sketch, mine is 1.3.2 (v42), what's yours -JacobBurkhart

*Your right! Sorry, don't know what I was thinking. Thanks for clearing that up. --RyanBates*

