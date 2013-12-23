---
layout: page
title: BetterTruncatingStringsInTableView
---

What we'd really like to see is NSLineBreakByTruncatingMiddle to start working someday. My guess is we'll have to wait for Panther. ***(Yup, it works now.)*** Meanwhile, this doesn't really do what **I** needed to do in the various tables and outlines in SpellCatcherX (of files, words, other things - there are quite a few in the app). Cocoa apps need to be able to draw strings that don't fit in a given width just like the Finder does - squishing the text as far as it can, then truncating in the *middle* if necessary.

In the first (circa 2003) version of SpellCatcherX, I used Carbon's TruncateThemeText. That works fine as long as the NSFont you're drawing in matches up with an available ThemeFontID. But I really wanted this to work in any font/size. The easiest way seemed to use ATSUI to draw the text. No big deal, I come from the world of Carbon (SpellCatcherX is my first Cocoa product). Use Carbon APIs when you need to - sometimes you must! No problem, so I learned a little (more than I already did) about ATSUI and got this to (mostly, with restrictions and caveats) work quite nicely. Additionally, ATSUI (well, with most fonts) deals properly with precomposed/decomposed Unicode (a potential issue with file names). http://goo.gl/OeSCu

You could probably do this with NSLayoutManager, doing the calculations and drawing the glyphs yourself, but why re-invent the wheel?

So with this category on NSCell, and with appropriate subclassing of NSTextFieldCell (or any NSCell that can draw text) you can have **non-editable, single-line** table column dataCells that squish/truncate text as they're resized, **non-editable/selectable, single-line** (see comments in the code) NSTextFields that will do the same if they're not wide enough for the text. Generally you'd call this from your -drawInteriorWithFrame:inView: in your NSTextFieldCell subclass.

Anyway, here goes. If you end up using it in your product, all I ask is you take a look at SpellCatcherX and buy a copy if you like it...

<code>
    
//
//  NSCell-Extensions.m
//  Spell Catcher X
//
//  Created by Evan Gross on Sat May 03 2003.
//  Copyright (c) 2001-2003 Rainmaker Research Inc. All rights reserved.
//
//  A couple of snippets/techniques extracted from various Apple samples and
//  from OmniAppKit.
//
//  Please do not remove this notice from the source code if you decide to use
//  it in your product (or some form of it).
//
//  Posted as solve-a-general-Cocoa-problem and shamelessly-plug-my-own-product 'ware.
//
//  Revision History:
//
//  Wed Jun 11 2003 EMG Version 1.1
//      - Can deal with synthesized oblique/italic fonts (Helvetica, Courier)
//      - Performance improvements:
//          + Re-use ATSUTextLayout object, as per "Guidelines for Using ATSUI".
//          + Only modify the ATSStyle object if the font or color has changed
//            from the previous call.
//          + Changes to use the user's RGB colorspace, and to re-use it instead
//            of creating/releasing a new CGColorSpaceRef with every call.

#import <NSCell-Extensions.h>

#import <Carbon/Carbon.h>

// ---------------------------------------------------------------------------

@implementation NSCell (RRIExtensions)

// ---------------------------------------------------------------------------

enum {
    //  Indices of ATSUStyle attributes.
    kATSUFontMatrixTagIndex     = 0,
    kATSUFontTagIndex,
    kATSUSizeTagIndex,
    kATSUQDBoldfaceTagIndex,

    kATSURGBAlphaColorTagIndex  = 0,

    //  Indices of ATSUTextLayout line layout attributes.
    kATSUCGContextTagIndex      = 0,
    kATSULineWidthTagIndex,
    kATSULineFlushFactorTagIndex,
    kATSULineTruncationTagIndex,
    kATSULineLayoutOptionsTagIndex,

    //  Size of stack buffer for the string's characters.
    kCharacterBufferSize        = 512,
};

// ---------------------------------------------------------------------------
//  Draw the text in an NSCell to get nice squished or truncated-in-the-middle
//  text like the Finder does. The shipping version of Spell Catcher X uses
//  Carbon's TruncateThemeText to accomplish this, but that only works if you're
//  drawing using a font that has a corresponding ThemeFontID. This (totally
//  new) method works with *almost* any NSFont that ATSFontFindFromPostScriptName()
//  can find a match for. Why *almost*? Well, it seems that there are certain
//  fonts out there that don't work well with the code below. Whether these fonts
//  are incorrectly built somehow or this code is buggy isn't something I
//  can't definitively state at this point in time. Problems vary from badly-rendered
//  "squished" text to outright crashes deep inside ATSUI. Two examples:
//      Try resizing a NSTableColumn with a dataCell class that uses this method with:
//          1. Helvetica (any typeface, any size) when the text contains
//             words separated by spaces. The result is that the spaces are
//             squished "too tightly" and the surrounding glyphs overlap.
//          2. Thornburi will crash when squished.
//          3. Helvetica-Oblique? There IS NO Helvetica Oblique!
//             Cocoa synthesizes it, so we have to as well.
//             NOTE: We simply skew the font matrix by kATSItalicQDSkew.
//             The result isn't *quite* the same as whatever Cocoa does.
//
//  NOTE: This works in flipped controlview's. Hasn't been tested in
//  non-flipped views.
//  NOTE: This works with left-to-right writing systems. Hasn't been
//  tested with right-left or bi-di.
//  NOTE: I have no idea if this works correctly when printing.
//
//  Returns NO if an error occurred, so you can call super to draw if something
//  goes wrong (and it can).

- (BOOL)drawTruncatedText:(NSString *)text
                  inFrame:(NSRect)cellFrame
                usingFont:(NSFont *)font
               usingColor:(NSColor *)color
{
    BOOL    didDrawViaATSUI = NO;

    //  Drawing offsets/insets.
    //  Used when calculating line width and the position for drawing text.
    //  NOTE: These are somewhat (or completely) arbitrarily chosen to:
    //      a) Draw the text properly vertically centered in the cell,
    //      b) Just generally look reasonably good.
    //  IMPORTANT: These values along with the calculations below DO NOT
    //  draw the text in the same place that the field editor does. So if
    //  the cell is either editable or selectable, you will get incorrect
    //  results if you use this code as-is.
    //  DISCLAIMER: If you require selectable or editable NSTextField-type
    //  cells, either:
    //      a) Don't use this in your NSCell subclass, or
    //      b) Take the challenge to try to figure out how the field editor
    //         calculates where to draw when editing/selecting a cell AND
    //         THEN how to calculate/convert the floating point NSView
    //         coordinates to the ATSUTextMeasurement (Fixed - FixMath.h)
    //         values used by ATSUI, or
    //      c) Find some other way to make it work AND THEN
    //      d) PLEASE tell me how you did either b) or c).
    //
    const float kCellFrameInset             = 1.0;
    const float kArbitraryHorizontalOffset  = 2.0;
    const float kArbitraryVerticalOffset    = 0.0;

    //  We re-use these (see Guidelines for Using ATSUI).
    static ATSUStyle        sATSStyle = nil;
    static ATSUTextLayout   sATSTextLayout = nil;

    //  We cache the last-used font and color so we only update
    //  style attributes when needed.
    static NSFont   *sLastUsedFont = nil;
    static NSColor  *sLastUsedColor = nil;
    static BOOL     sSynthesizeItalic = NO;

    //  We also cache the fill colorspace we use.
    //  NOTE: I'm not sure if this causes a problem if
    //  the user changes their Default RGB ColorSync profile.
    static CGColorSpaceRef  sUserRGBCSRef = nil;

    //  Do nothing with zero-length text or a too-narrow cellFrame.
    if ([text length] > 0 && NSWidth(cellFrame) > 2 * kCellFrameInset)
    {
        OSStatus        status = noErr;
        NSMutableData   *theData = nil;

        //  Create style object if necessary.
        if (sATSStyle == nil)
        {
            //  Create the text style.
            status = ATSUCreateStyle(&sATSStyle);

            //  Use your favorite assert/require/exception handling macros
            //  or technique if you don't like this. Best to have only one
            //  "place of exit" in code that's more than a 4 or 5 lines long.
            if (status != noErr)
                goto done;
        }

        //  Create text layout object if necessary.
        if (sATSTextLayout == nil)
        {
            status = ATSUCreateTextLayout(&sATSTextLayout);
            if (status != noErr)
                goto done;
        }

        //  Has the font changed?
        if (![font isEqual:sLastUsedFont])
        {
            //  Three parallel arrays for setting up style attributes.
            const ATSUAttributeTag  theFontStyleTags[] = { kATSUFontMatrixTag,
                                        kATSUFontTag, kATSUSizeTag, kATSUQDBoldfaceTag };
            const ByteCount         theFontStyleSizes[] = { sizeof(CGAffineTransform),
                                        sizeof(ATSUFontID), sizeof(Fixed), sizeof(Boolean) };
            ATSUAttributeValuePtr   theFontStyleValues[] = { nil, nil, nil, nil };

            //  Initialize font-related stuff.
            Boolean         theQDBoldfaceValue = false;
            ATSUFontID      theFMFont = kInvalidFont;
            NSFontTraitMask theFontTraits = [[NSFontManager sharedFontManager] traitsOfFont:font];
            NSFont          *theConvertedFont = nil;

            //  Assume we don't have to synthesize an italic typeface.
            sSynthesizeItalic = NO;

            //  Try to get an ATSFontRef from the font's PostScript name.
            ATSFontRef  theATSFontRef = ATSFontFindFromPostScriptName(
                                            (CFStringRef) [font fontName],
                                            kATSOptionFlagsDefault);

            //  If unsuccessful, it may be the case that this is a "special" font
            //  (like an Oblique variation of Helvetica or Courier) that doesn't
            //  actually exist but Cocoa synthesizes.
            //  NOTE: We only try to deal with synthesized italic (oblique) and/or bold.
            //  The first thing we do is try to find the unitalicized version
            //  by PostScript name.
            if (theATSFontRef == kATSFontRefUnspecified &&
                ((theFontTraits & NSItalicFontMask) != 0))
            {
                //  We have to synthesize italic by skewing the font matrix.
                //  NOTE: We can't seem to use kATSUQDItalicTag due to (supposedly)
                //  an ATSUI bug where italics are drawn with the wrong slant
                //  (i.e. to the left rather than to the right).
                sSynthesizeItalic = YES;

                //  See if removing the italic trait lets us find via postscript name.
                //  This means that if there's a bold variation for a bold-oblique font,
                //  we'll use it instead of synthesizing bold as well.
                theConvertedFont = [[NSFontManager sharedFontManager] convertFont:font
                                                        toHaveTrait:NSUnitalicFontMask];
                theATSFontRef = ATSFontFindFromPostScriptName(
                                            (CFStringRef) [theConvertedFont fontName],
                                            kATSOptionFlagsDefault);
            }

            //  Still no font? Try removing any bold trait.
            if (theATSFontRef == kATSFontRefUnspecified && ((theFontTraits & NSBoldFontMask) != 0))
            {
                //  We have to synthesize bold via the kATSUQDBoldfaceTag attribute.
                theQDBoldfaceValue = true;

                //  See if removing the bold trait lets us find via postscript name.
                theConvertedFont = [[NSFontManager sharedFontManager] convertFont:theConvertedFont
                                                toHaveTrait:NSUnboldFontMask];
                theATSFontRef = ATSFontFindFromPostScriptName(
                                            (CFStringRef) [theConvertedFont fontName],
                                            kATSOptionFlagsDefault);
            }

            //  STILL no font? Yikes. Not quite sure why we'd get here,
            //  at least with the default set of OS X fonts and the font
            //  that the font panel (in Cocoa) would hand you.
            //  Just punt and see if we can find via the family name.
            if (theATSFontRef == kATSFontRefUnspecified)
            {
                //  We're toast if we can't even get the family.
                ATSFontFamilyRef    theATSFontFamilyRef = ATSFontFamilyFindFromName(
                                            (CFStringRef)[font familyName],
                                            kATSOptionFlagsDefault);
                if (theATSFontFamilyRef == kATSFontFamilyRefUnspecified)
                {
                    status = kFMInvalidFontFamilyErr;
                    goto done;
                }

                //  Get the "normal" style instance.
                FMFontFamily    theFMFontFamily = FMGetFontFamilyFromATSFontFamilyRef(
                                                    theATSFontFamilyRef);
                FMFontStyle     theFMFontStyle;
                status = FMGetFontFromFontFamilyInstance(theFMFontFamily,
                            normal, &theFMFont, &theFMFontStyle);
                if (status != noErr)
                    goto done;
            }

            //  Get the ATSUFontID (FMFont) from the ATSFontRef (what kATSUFontTag wants).
            else
            {
                theFMFont = FMGetFontFromATSFontRef(theATSFontRef);
            }

            //  We 'flip the coordinate system' in Y so we have QD style coordinates.
            CGAffineTransform   theTransform = CGAffineTransformMakeScale(1.0, -1.0);

            //  If we're synthesizing italic/oblique, do it by skewing.
            if (sSynthesizeItalic)
                theTransform.c = Fix2X(kATSItalicQDSkew);

            //  Set the font matrix.
            theFontStyleValues[kATSUFontMatrixTagIndex] = &theTransform;

            //  Set the font.
            theFontStyleValues[kATSUFontTagIndex] = &theFMFont;

            //  Set the size.
            Fixed   thePointSize = X2Fix([font pointSize]);
            theFontStyleValues[kATSUSizeTagIndex] = &thePointSize;

            //  Set the kATSUQDBoldfaceTag tag.
            theFontStyleValues[kATSUQDBoldfaceTagIndex] = &theQDBoldfaceValue;

            //  Set font-specific style attributes.
            status = ATSUSetAttributes(sATSStyle,
                            sizeof(theFontStyleTags) / sizeof(ATSUAttributeTag),
                            theFontStyleTags, theFontStyleSizes, theFontStyleValues);
            if (status != noErr)
                goto done;
        }

        //  Set the kATSURGBAlphaColorTag if the color's changed.
        if (![color isEqual:sLastUsedColor])
        {
            //  Three parallel arrays for setting up style attributes.
            const ATSUAttributeTag  theStyleColorTags[] = { kATSURGBAlphaColorTag };
            const ByteCount         theStyleColorSizes[] = { sizeof(ATSURGBAlphaColor) };
            ATSUAttributeValuePtr   theStyleColorValues[] = { nil };

            //  Convert color to NSCalibratedRGBColorSpace so we can
            //  extract the components for setting the ATSURGBAlphaColor.
            NSColor *theTextColor = [color colorUsingColorSpaceName:NSCalibratedRGBColorSpace
                                                             device:nil];

            //  Setup an ATSURGBAlphaColor from theTextColor's components.
            ATSURGBAlphaColor   theATSRGBColor;
            [theTextColor getRed:&theATSRGBColor.red
                           green:&theATSRGBColor.green
                            blue:&theATSRGBColor.blue
                           alpha:&theATSRGBColor.alpha];
            theStyleColorValues[kATSURGBAlphaColorTagIndex] = &theATSRGBColor;

            //  Set specific color-related style attributes.
            status = ATSUSetAttributes(sATSStyle,
                            sizeof(theStyleColorTags) / sizeof(ATSUAttributeTag),
                            theStyleColorTags, theStyleColorSizes, theStyleColorValues);
            if (status != noErr)
                goto done;
        }

        //  Buffer for the characters.
        unsigned int    theLength = [text length];
        unichar         theBuffer[kCharacterBufferSize];
        unichar         *theChars = theBuffer;

        //  If the length is too long for our default buffer, allocate memory to
        //  hold the characters (slower).
        if (theLength * sizeof(unichar) > sizeof(theBuffer))
        {
            theData = [[NSMutableData alloc] initWithLength:theLength * sizeof(unichar)];

            theChars = [theData mutableBytes];
        }

        //  Get the characters. (CFStringGetCharacters is faster than -[NSString getCharacters]).
        CFStringGetCharacters((CFStringRef) text, CFRangeMake(0, theLength), theChars);

        //  Set the text for the layout.
        status = ATSUSetTextPointerLocation(sATSTextLayout, theChars,
                        kATSUFromTextBeginning, kATSUToTextEnd, theLength);
        if (status != noErr)
            goto done;

        //  Set the style for the layout.
        status = ATSUSetRunStyle(sATSTextLayout, sATSStyle, 0, theLength);
        if (status != noErr)
            goto done;

        //  Three parallel arrays for setting up layout attributes.
        const ATSUAttributeTag  theLayoutTags[] = { kATSUCGContextTag,
                                    kATSULineWidthTag, kATSULineFlushFactorTag,
                                    kATSULineTruncationTag, kATSULineLayoutOptionsTag };
        const ByteCount         theLayoutSizes[] = { sizeof(CGContextRef),
                                    sizeof(ATSUTextMeasurement), sizeof(Fract),
                                    sizeof(ATSULineTruncation), sizeof(ATSLineLayoutOptions) };
        ATSUAttributeValuePtr   theLayoutValues[] = { nil, nil, nil, nil, nil };

        //  Assign the CGContext to the layout so the text is imaged into the CG Context supplied.
        NSGraphicsContext   *theContext = [NSGraphicsContext currentContext];
        CGContextRef        theCGContextRef = [theContext graphicsPort];
        theLayoutValues[kATSUCGContextTagIndex] = &theCGContextRef;

        //  If we're synthesizing italic, we have to account for the extra
        //  width the text might occupy manually (ATSUDrawText doesn't seem to,
        //  and might draw beyond the basic line width). We do this by
        //  applying the font transform to the width we *want* to draw in
        //  and subtracting the excess from theLineWidth.
        //  NOTE: I'm not entirely sure this is the best way to calculate
        //  this value, but it appears to work ok...
        float   theExtraSkewWidth = 0.0;
        if (sSynthesizeItalic)
        {
        #ifdef DEFINEMEWHENATSUICANDOTHIS
            //  NOTE: ATSUGetAttribute doesn't work with kATSUFontMatrixTag! (ATS-102.1~2)
            CGAffineTransform   theFontTransform;
            ByteCount           theActualValueSize;
            if (ATSUGetAttribute(sATSStyle, kATSUFontMatrixTag,
                            sizeof(CGAffineTransform), &theFontTransform,
                            &theActualValueSize) == noErr)
            {
        #else
                //  Have to construct the transform manually due to the above bug.
                //  We *could* cache this, but I don't think performance is a
                //  problem here.
                CGAffineTransform   theFontTransform = CGAffineTransformMakeScale(1.0, -1.0);
                theFontTransform.c = Fix2X(kATSItalicQDSkew);
        #endif

                CGSize  theOldSize = CGSizeMake(NSWidth(cellFrame) - 2 * kCellFrameInset,
                                                NSHeight(cellFrame));
                CGSize  theNewSize = CGSizeApplyAffineTransform(theOldSize, theFontTransform);

                theExtraSkewWidth = theNewSize.width - theOldSize.width;

        #ifdef DEFINEMEWHENATSUICANDOTHIS
            }
        #endif
        }

        //  Set the line width.
        Fixed   theLineWidth = X2Fix(NSWidth(cellFrame) - 2 * kCellFrameInset - theExtraSkewWidth);
        theLayoutValues[kATSULineWidthTagIndex] = &theLineWidth;

        //  Set the alignment. Note that we don't deal with
        //  NSNaturalTextAlignment or NSJustifiedTextAlignment.
        //  While we *could* do something, note that the ATSUI
        //  alignment and justification attributes offer much
        //  more than NSJustifiedTextAlignment. Not that applicable
        //  to this particular problem (justified text, that is)
        //  anyway. Exercise for the reader.
        Fract    theATSAlignment;
        switch ([self alignment])
        {
            case NSRightTextAlignment:
                theATSAlignment = kATSUEndAlignment;
                break;

            case NSCenterTextAlignment:
                theATSAlignment = kATSUCenterAlignment;
                break;

            default:
                theATSAlignment = kATSUStartAlignment;
                break;
        }

        theLayoutValues[kATSULineFlushFactorTagIndex] = &theATSAlignment;

        //  Set the truncation.
        SInt32    theTruncation = kATSUTruncateMiddle;
        theLayoutValues[kATSULineTruncationTagIndex] = &theTruncation;

        //  Set line layout options. We don't like the way hanging
        //  punctuation looks in tableview's.
        ATSLineLayoutOptions    theLineLayoutOptions = kATSLineHasNoHangers;
        theLayoutValues[kATSULineLayoutOptionsTagIndex] = &theLineLayoutOptions;

        //  Set layout controls.
        status = ATSUSetLayoutControls(sATSTextLayout,
                        sizeof(theLayoutTags) / sizeof(ATSUAttributeTag),
                        theLayoutTags, theLayoutSizes, theLayoutValues);
        if (status != noErr)
            goto done;

        //  We want font matching to occur.
        status = ATSUSetTransientFontMatching(sATSTextLayout, true);
        if (status != noErr)
            goto done;

        //  Get the ascent and descent so we can calculate the lineheight.
        //  We can ignore the return value, as ATSUI gives us a default
        //  when not explicitly set.
        ByteCount              theActualSize;
        ATSUTextMeasurement    theAscent;
        ATSUGetLineControl(sATSTextLayout, 0, kATSULineAscentTag,
                    sizeof(ATSUTextMeasurement), &theAscent, &theActualSize);

        ATSUTextMeasurement    theDescent;
        ATSUGetLineControl(sATSTextLayout, 0, kATSULineDescentTag,
                    sizeof(ATSUTextMeasurement), &theDescent, &theActualSize);
        float    theLineHeight = Fix2X(theAscent) + Fix2X(theDescent);

        //  ATSUDrawText might not like the current fill colorspace,
        //  (if it's set to a pattern colorspace), so set it manually
        //  so set it manually to user RGB, creating if necessary.
        if (sUserRGBCSRef == nil)
            sUserRGBCSRef = CGColorSpaceCreateWithName(kCGColorSpaceUserRGB);

        CGContextSaveGState(theCGContextRef);
        CGContextSetFillColorSpace(theCGContextRef, sUserRGBCSRef);

        //  Sometimes, if you hand ATSUDrawText a long enough string
        //  (not sure exactly how to quantify "long enough") it will
        //  draw beyond the line width set above (semi-correctly,
        //  truncation seems to be performed). So we have to clip
        //  to avoid drawing outside of the portion of cellFrame
        //  we use.
        CGContextClipToRect(theCGContextRef,
                CGRectMake(NSMinX(cellFrame) + kArbitraryHorizontalOffset,
                           NSMinY(cellFrame),
                           NSWidth(cellFrame) - 2 * kCellFrameInset,
                           NSHeight(cellFrame)));

        //  Draw the text, centered vertically, offset slightly to the right
        //  (NOT the same amount that NSTextFieldCell actually uses!).
        status = ATSUDrawText(sATSTextLayout, kATSUFromTextBeginning, kATSUToTextEnd,
                        X2Fix(NSMinX(cellFrame) + kArbitraryHorizontalOffset),
                        X2Fix(NSMidY(cellFrame) - theLineHeight / 2.0
                         + Fix2X(theDescent) + kArbitraryVerticalOffset));

        //  Restore/release things.
        CGContextRestoreGState(theCGContextRef);

    done:

        //  Dispose of things.
        [theData release];

        didDrawViaATSUI = (status == noErr);

        //  Only cache the font and color if there wasn't an error.
        if (didDrawViaATSUI)
        {
            if (![font isEqual:sLastUsedFont])
            {
                [sLastUsedFont release];

                sLastUsedFont = [font copyWithZone:[self zone]];
            }

            if (![color isEqual:sLastUsedColor])
            {
                [sLastUsedColor release];

                sLastUsedColor = [color copyWithZone:[self zone]];
            }
        }

        //  In case something went wrong.
        //if (status != noErr)
            //NSLog(@"YIKES:drawTruncatedText status:%d", status);
    }

    return didDrawViaATSUI;
}

@end

</code>

Have fun! Fixes/modifications/comments/preformance enhancements gratefully accepted.

Evan Gross
Rainmaker Research Inc.

----

This does't seem to work, or return an error, if the cellFrame.origin.x is around 32768 or higher. this can happen when drawing table views with many items... 2-3 thousand. i'm guessing that an somewhere a float is getting cast to an int, but i don't know much about carbon and can't find the error. also it doesn't seem to be direcly related to cellFrame.origin.x going above 32768 (i see the problem at 32761) so maybe the problem is when cellFrame.origin.x + cellFrame.size.height > 32768.

if anyone can spot it please let me know.

JesseGrosjean

----

From your description of the problem I looked at references to cellFrame, which is an NSRect. NSRect normally uses floating point so it doesn't have a problem with reasonably large coordinates. But we're not dealing with just Cocoa APIs here; it seems that in some places coordinates within the NSRect are being converted to numbers of type Fixed.

The problem is that Fixed is a 16-bit integer quantity. Actually it's 16 bits of integer and 16 bits of fraction, but anyway, the point is you can't represent numbers greater than plus or minus 2^16. InsideMacintosh describes the Fixed type.

http://developer.apple.com/documentation/mac/OSUtilities/OSUtilities-39.html#MARKER-9-36

There are three places in the above code where X2Fix is used to convert a float into a Fixed. They're all potential failure points. By failure I mean bogus results - if the value goes beyond the legal range for a Fixed, it'll wind up passing incorrect values for drawing the text, and you'll get unpredictable results. Missing or incorrectly offset text, most likely.


*This will fail if [font pointSize] is greater than 32767. Unlikely, but possible.
    
//  Set the size.
Fixed   thePointSize = X2Fix([font pointSize]);
theFontStyleValues[kATSUSizeTagIndex] = &thePointSize; 

*This will fail if the cell's width is greater than about 32767. Slightly more possible than the above, but still not typical.
    
//  Set the line width.
Fixed   theLineWidth = X2Fix(NSWidth(cellFrame) - 2 * kCellFrameInset - theExtraSkewWidth);
theLayoutValues[kATSULineWidthTagIndex] = &theLineWidth; 

*This will fail if either of the cell's X and Y coordinates are beyond (or too close to the boundary of) the range [-32767,32767].
    
//  Draw the text, centered vertically, offset slightly to the right
//  (NOT the same amount that NSTextFieldCell actually uses!).
status = ATSUDrawText(sATSTextLayout, kATSUFromTextBeginning, kATSUToTextEnd,
                X2Fix(NSMinX(cellFrame) + kArbitraryHorizontalOffset),
                X2Fix(NSMidY(cellFrame) - theLineHeight / 2.0
                 + Fix2X(theDescent) + kArbitraryVerticalOffset));



So that's at least part of the problem, which it sounds like you're hitting.  I'd solve the first problem by pinning. Do a range check on [font pointSize], and pin it to the legal range. You won't be able to use font sizes larger than 32767 points, but that seems like it'll probably be okay most of the time.

    
//  Set the size.
float   thePointSizeFloat = [font pointSize];
Fixed   thePointSize = X2Fix(MAX(MIN(thePointSizeFloat,32767.0f),-32767.0f));
theFontStyleValues[kATSUSizeTagIndex] = &thePointSize; 


The simplest way to solve the second problem would also be by pinning. In some cases this behavior might be incorrect, if you routinely expect to draw very very very long strings and don't want to limit the drawable line width. You'd probably have to break the line drawing up into separate calls if you wanted to do that. 

    
//  Set the line width.
float   theLineWidthFloat = NSWidth(cellFrame) - 2 * kCellFrameInset - theExtraSkewWidth;
Fixed   theLineWidth = X2Fix(MAX(MIN(theLineWidthFloat,32767.0f),-32767.0f));
theLayoutValues[kATSULineWidthTagIndex] = &theLineWidth; 


The third problem is harder. The x and y arguments that you use to tell ATSUI where to draw are of type ATSUTextMeasurement, which is a Fixed, so it cannot refer to numbers outside of [-32767,32767]. So there's no way to pass in a number greater than 32767 to this routine.

One possible trick to use is changing the origin before you draw. You can't use Carbon to do that because Carbon's drawing engine (QuickDraw) is similarly limited to 16-bit coordinates. The saving grace might be that ATSUDrawText does say that it will draw into the current Quartz graphics context. So changing the origin of the Quartz context might make it work.

This is straight off the top of my head and totally untested, but maybe something like:

    
//  Draw the text, centered vertically, offset slightly to the right
//  (NOT the same amount that NSTextFieldCell actually uses!).
//  Change the context origin before drawing so that we can
//  draw at coordinates beyond 2^16.
float   xpos = NSMinX(cellFrame) + kArbitraryHorizontalOffset;
float   ypos = NSMidY(cellFrame) - theLineHeight / 2.0 + Fix2X(theDescent) + kArbitraryVerticalOffset;
CGContextTranslateCTM(theCGContextRef,xpos,ypos);
status = ATSUDrawText(sATSTextLayout, kATSUFromTextBeginning, kATSUToTextEnd,
    (Fixed)0, (Fixed)0);


This modification might need some tweaking but I think it's the general idea. If this works for you, please post the actual code you used! :-)

----

Ironically, speaking of 32K limits, this page is very close to the wiki's 32K size limit so please direct further comments, etc, to BetterTruncatingStringsInTableViewDiscussion.

