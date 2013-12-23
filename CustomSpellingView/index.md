---
layout: page
title: CustomSpellingView
---

This article describes how to support spell checking and grammer checking in a custom view. This view supports checking spelling using the Spelling pane and the Edit menu.

There aren't a lot of examples of how to do this. The best one I could find was WebKit:
http://trac.webkit.org/browser/trunk/WebKit/mac/WebCoreSupport/WebEditorClient.mm

 --SaileshAgrawal

----

    
#import <Cocoa/Cocoa.h>

@interface MyView : NSView
{
  NSString *document;
  NSRange selection;
  NSInteger documentTag;
  NSArray *cachedSpellCheckResults;
  NSInteger misspellingIndex;
  NSInteger misspellingCount;
}
@end



    
#import "MyView.h"


static BOOL sContinuousSpellCheckingEnabled = YES;
static BOOL sGrammarCheckingEnabled = YES;


@interface MyView ()

@property (retain) NSString *document;
@property (retain) NSArray *cachedSpellCheckResults;

@end


@implementation MyView

@synthesize document;
@synthesize cachedSpellCheckResults;

- (void)dealloc
{
  [self setDocument:nil];
  [self setCachedSpellCheckResults:nil];
  [super dealloc];
}

- (void)setDocument:(NSString *)newDocument
{
  [newDocument retain];
  [document release];
  document = newDocument;
  [self setCachedSpellCheckResults:nil];
}

- (NSArray *)spellCheckResults
{
  if (!cachedSpellCheckResults) {
    NSTextCheckingType types = 0;
    if (sContinuousSpellCheckingEnabled)
      types |= NSTextCheckingTypeSpelling;
    if (sGrammarCheckingEnabled)
      types |= NSTextCheckingTypeGrammar;
    [self setCachedSpellCheckResults:[[NSSpellChecker sharedSpellChecker]
                   checkString:document
                         range:NSMakeRange(0, [document length])
                         types:types
                       options:nil
        inSpellDocumentWithTag:documentTag
                   orthography:NULL
                     wordCount:NULL]];
  }
  return cachedSpellCheckResults;
}

- (void)awakeFromNib
{
  [self setDocument:[NSMutableString stringWithString:
      @"I is a goode reader.\nHelllo worrld this is someething else."]];
  documentTag = [NSSpellChecker uniqueSpellDocumentTag];
}

- (BOOL)acceptsFirstResponder
{
  return YES;
}

- (BOOL)becomeFirstResponder
{
  [self setNeedsDisplay:YES];
  return YES;
}

- (BOOL)resignFirstResponder
{
  [self setNeedsDisplay:YES];
  return YES;
}

- (void)drawRect: (NSRect)rect
{
  NSRect bounds = [self bounds];
  [[NSColor lightGrayColor] set];
  NSRectFill(bounds);

  if ([self window] firstResponder] isEqual:self]) {
    [[[[NSColor blueColor] set];
    NSFrameRectWithWidth(bounds, 4);
  }

  NSDictionary *attributes = [NSDictionary dictionaryWithObjectsAndKeys:
      [NSFont systemFontOfSize:20], NSFontAttributeName,
      nil];
  NSDictionary *spellingAttributes = [NSDictionary dictionaryWithObjectsAndKeys:
      [NSNumber numberWithInt:NSUnderlineStyleSingle | NSUnderlinePatternDot], NSUnderlineStyleAttributeName,
      [NSColor redColor], NSUnderlineColorAttributeName,
      nil];
  NSDictionary *grammarAttributes = [NSDictionary dictionaryWithObjectsAndKeys:
      [NSNumber numberWithInt:NSUnderlineStyleSingle | NSUnderlinePatternDot], NSUnderlineStyleAttributeName,
      [NSColor greenColor], NSUnderlineColorAttributeName,
      nil];

  NSMutableAttributedString *text = [[[NSMutableAttributedString alloc]
      initWithString:document
          attributes:attributes] autorelease];

  for (NSTextCheckingResult *result in [self spellCheckResults]) {
    if ([result resultType] & NSTextCheckingTypeSpelling) {
      [text addAttributes:spellingAttributes range:[result range]];
    }
    if ([result resultType] & NSTextCheckingTypeGrammar) {
      for (NSDictionary *details in [result grammarDetails]) {
        NSRange range = details objectForKey:[[NSGrammarRange] rangeValue];
        range.location += [result range].location;
        [text addAttributes:grammarAttributes range:range];
      }
    }
  }

  if (selection.length > 0) {
    NSDictionary *selAttributes = [NSDictionary dictionaryWithObjectsAndKeys:
        [NSColor selectedTextBackgroundColor], NSBackgroundColorAttributeName,
        nil];
    [text addAttributes:selAttributes
                  range:selection];
  }

  NSRect textFrame;
  textFrame.size = [text size];
  textFrame.origin.x = roundf((bounds.size.width - textFrame.size.width) / 2.0);
  textFrame.origin.y = roundf((bounds.size.height - textFrame.size.height) / 2.0);
  [text drawInRect:textFrame];
}

- (void)setSelection: (NSRange)range
{
  selection = range;
  [self setNeedsDisplay:YES];
}

- (BOOL)advanceToNextMisspelling: (NSTextCheckingResult **)outResult
                         details: (NSDictionary **)outDetails
                           range: (NSRange *)outRange
{
  NSUInteger findLocation = selection.location + selection.length;
  for (NSTextCheckingResult *result in [self spellCheckResults]) {
    if ([result resultType] & NSTextCheckingTypeSpelling) {
      if ([result range].location >= findLocation) {
        *outResult = result;
        *outDetails = nil;
        *outRange = [result range];
        return YES;
      }
    }
    if ([result resultType] & NSTextCheckingTypeGrammar) {
      for (NSDictionary *details in [result grammarDetails]) {
        NSRange range = details objectForKey:[[NSGrammarRange] rangeValue];
        range.location += [result range].location;
        if (range.location >= findLocation) {
          *outResult = result;
          *outDetails = details;
          *outRange = range;
          return YES;
        }
      }
    }
  }

  return NO;
}

- (IBAction)checkSpelling:(id)sender
{
  NSTextCheckingResult *result = nil;
  NSDictionary *details = nil;
  NSRange range;
  if ([self advanceToNextMisspelling:&result
                             details:&details
                               range:&range]) {
    if ([result resultType] & NSTextCheckingTypeSpelling) {
      [[NSSpellChecker sharedSpellChecker]
          updateSpellingPanelWithMisspelledWord:[document substringWithRange:range]];
    } else if ([result resultType] & NSTextCheckingTypeGrammar) {
        [[NSSpellChecker sharedSpellChecker]
            updateSpellingPanelWithGrammarString:[document substringWithRange:range]
                                          detail:details];
    }
    [self setSelection:range];
  } else {
    [[NSSpellChecker sharedSpellChecker]
        updateSpellingPanelWithMisspelledWord:@""];
    [self setSelection:NSMakeRange(0,0)];
  }
}

- (IBAction)changeSpelling:(id)sender
{
  NSString* newWord = sender selectedCell] stringValue];
  if (newWord != nil) {
    [self setDocument:
      [document stringByReplacingCharactersInRange:selection
                                        withString:newWord;
    NSRange range;
    range.location = selection.location + [newWord length];
    range.length = 0;
    [self setSelection:range];
  }
}

- (IBAction)ignoreSpelling:(id)sender
{
  NSString* wordToIgnore = [sender stringValue];
  if (wordToIgnore != nil) {
    // TODO This doesn't ignore grammar mistakes.
    [[NSSpellChecker sharedSpellChecker]
                    ignoreWord:wordToIgnore
        inSpellDocumentWithTag:documentTag];
    NSRange range;
    range.location = selection.location + [wordToIgnore length];
    range.length = 0;
    [self setSelection:range];
    [self setCachedSpellCheckResults:nil];
  }
}

- (IBAction)showGuessPanel:(id)sender
{
  if (YES) {
    [[[NSSpellChecker sharedSpellChecker] spellingPanel]
        performSelectorOnMainThread:@selector(makeKeyAndOrderFront:)
                         withObject:nil
                      waitUntilDone:YES];
    [self checkSpelling:nil];
  } else {
    [[[NSSpellChecker sharedSpellChecker] spellingPanel]
        performSelectorOnMainThread:@selector(close)
                         withObject:nil
                      waitUntilDone:YES];
  }
}

- (IBAction)toggleContinuousSpellChecking:(id)sender
{
  sContinuousSpellCheckingEnabled = !sContinuousSpellCheckingEnabled;
  [self setNeedsDisplay:YES];
}

- (IBAction)toggleGrammarChecking:(id)sender
{
  sGrammarCheckingEnabled = !sGrammarCheckingEnabled;
  [self setNeedsDisplay:YES];
}

@end

