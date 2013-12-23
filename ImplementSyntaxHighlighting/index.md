---
layout: page
title: ImplementSyntaxHighlighting
---

I want to implement syntax highlighting in a text editor. Do I have to subclass NSTextField or something similar?

----

I've done this with a little perl text editor I've been using for a few years. Register for the NSTextStorageDidProcessEditingNotification notification to learn when text was modified, get the NSTextStorage object from the notification, use NSTextStorage's editedRange to figure out where the recent edit was, and then colorize the NSTextView accordingly. Here's a meaningless blurb:

    - (void)processEditing:(NSNotification *)notification
    {
        NSTextStorage *textStorage = [notification object];
        NSRange range = [textStorage editedRange];
    
        // colorize the proper NSTextView ... 
    }

I ended up subclassing NSTextStorage and NSTextView to perform other tasks, but that shouldn't be required in order to do syntax coloring.

----

 The following code listens to changes to a NSTextView and colorizes all instances of the word "Mike" in blue, FWIW. The example colorizes the entire document after every key-press. This becomes quite costly; a "real" editor will try to minimize the area it attempts to colorize. Xcode, for example, only colorizes text that's currently on-screen, and defers colorizing the rest of the document until you scroll through it.

I added a text view to the default window. I created an NSObject subclass called Controller, instantiated it, added "window" and "textview" outlets, and hooked the outlets up to the window and text view. Then I saved the files. Here's the controller definition:

Controller.h

    /* Controller */
    
    #import <Cocoa/Cocoa.h>
    
    @interface Controller : NSObject
    {
        IBOutlet id textview;
        IBOutlet id window;
    }
    @end

Controller.m

    #import "Controller.h"
    
    @implementation Controller
    
    - (void)awakeFromNib
    {
        textview textStorage] setDelegate:self];
    }
    
    - (void)textStorageDidProcessEditing:([[NSNotification *)notification
    {
        NSTextStorage *textStorage = [notification object];
        NSColor *blue = [NSColor blueColor];
        NSRange found, area;
        NSString *string = [textStorage string];
        unsigned int length = [string length];
    
        // remove the old colors
        area.location = 0;
        area.length = length;
        [textStorage removeAttribute:NSForegroundColorAttributeName range:area];
    
        // add new colors
        while (area.length) {
            found = [string rangeOfString:@"Mike" 
                                  options:NSCaseInsensitiveSearch 
                                    range:area];
            if (found.location == NSNotFound) break;
            [textStorage addAttribute:NSForegroundColorAttributeName
                                value:blue
                                range:found];
            area.location = NSMaxRange(found);
            area.length = length - area.location;
        }
    }
    
    @end

I'm not subclassing NSTextView or NSTextStorage; I register a delegate for the NSTextStorage object and listen to the proper notification. The NSTextStorage delegate is allowed to modify the storage's text attributes, so long as the delegate doesn't change the text's characters directly (this apparently includes switching from western to Kanji fonts).

I compiled the application, ran it, and watched my "mike" strings draw in blue. You should be able to get this little example up and running quickly, assuming you're familiar with using InterfaceBuilder.

Personally, I'd start by colorizing only the area around the actual edit, rather than colorizing the entire document w/ each pass. This means getting the edited area using NSTextStorage's editedRange, rolling back the range to a previous point (such as a word boundary), and rolling forward to a following point (such as a word boundary again) and colorize that edited range. My own perl editor does this and I've been fairly happy with the results.

If you want to try to get "just in time" syntax coloring to work, start by reading the documentation on all of the text classes, including NSTextView, NSTextStorage, NSTextContainer, and NSLayoutManager. Also read the TextArchitecture topics found here:

file:///Developer/Documentation/Cocoa/TasksAndConcepts/ProgrammingTopics/TextArchitecture/index.html

or here:

http://developer.apple.com/techpubs/macosx/Cocoa/TasksAndConcepts/ProgrammingTopics/TextArchitecture/index.html
http://developer.apple.com/documentation/Cocoa/Conceptual/TextArchitecture/index.html

This will be fairly difficult, probably involving a lot of subclassing and doing things "by hand". Even ProjectBuilder doesn't get this right all the time.

----

I've extended my previous program to emulate a simple on-the-fly spell checker. If you type a word that's not in the word dictionary the word is colored in red. Otherwise the word is black. The implementation doesn't support punctuation of any kind, including commas and periods, hyphenated words, or apostrophes.

The word dictionary comes from the system's /usr/share/dict/words dictionary that comes with Mac OS X.

Here's the controller definition:

Controller.h

    /* Controller */
    
    #import <Cocoa/Cocoa.h>
    
    @interface Controller : NSObject
    {
        IBOutlet id textview;
        IBOutlet id window;
    
        NSMutableDictionary *words;
    }
    @end

Controller.m

    #import "Controller.h"
    
    @implementation Controller
    
    - (void)awakeFromNib
    {
        NSScanner *scanner;
        NSString *word;
        NSString *file;
        NSCharacterSet *whiteSpaceSet;
    
        textview textStorage] setDelegate:self];
    
        // load our dictionary
        whiteSpaceSet = [[[NSCharacterSet whitespaceAndNewlineCharacterSet];
        words = [[NSMutableDictionary alloc] init];
        file = [NSString stringWithContentsOfFile:@"/usr/share/dict/words"];
        if (!file) return; // error
        scanner = [NSScanner scannerWithString:file];
        while (![scanner isAtEnd]) {
            BOOL ok;
            ok = [scanner scanUpToCharactersFromSet:whiteSpaceSet
                                         intoString:&word];
            if (ok)
                [words setObject:word forKey:word]; // simple hash
        }
    }
    
    - (void)textStorageDidProcessEditing:(NSNotification *)notification
    {
        NSTextStorage *textStorage = [notification object];
        NSString *string = [textStorage string];
        NSRange area = [textStorage editedRange];
        unsigned int length = [string length];
        NSRange start, end;
        NSCharacterSet *whiteSpaceSet;
        unsigned int areamax = NSMaxRange(area);
        NSRange found;
        NSColor *red = [NSColor redColor];
        NSString *word;
        
        // extend our range along word boundaries.
        whiteSpaceSet = [NSCharacterSet whitespaceAndNewlineCharacterSet];
        start = [string rangeOfCharacterFromSet:whiteSpaceSet
                                        options:NSBackwardsSearch
                                          range:NSMakeRange(0, area.location)];
        if (start.location == NSNotFound) {
            start.location = 0;
        }  else {
            start.location = NSMaxRange(start);
        }
        end = [string rangeOfCharacterFromSet:whiteSpaceSet
                                      options:0
                                        range:NSMakeRange(areamax, length - areamax)];
        if (end.location == NSNotFound)
            end.location = length;
        area = NSMakeRange(start.location, end.location - start.location);
        if (area.length == 0) return; // bail early
        
        // remove the old colors
        [textStorage removeAttribute:NSForegroundColorAttributeName range:area];
    
        // add new colors
        while (area.length) {
            // find the next word
            end = [string rangeOfCharacterFromSet:whiteSpaceSet
                                          options:0
                                            range:area];
            if (end.location == NSNotFound) {
                end = found = area;
            } else {
                found.length = end.location - area.location;
                found.location = area.location;
            }
            word = [string substringWithRange:found];
    
            // color as necessary
            if ([words objectForKey:word] == NULL) {
                [textStorage addAttribute:NSForegroundColorAttributeName
                                    value:red
                                    range:found];
            }
            
            // adjust our area
            areamax = NSMaxRange(end);
            area.length -= areamax - area.location;
            area.location = areamax;
        }
    }
    
    @end

The interface is the same as that in the "Mike" program with the addition of an InstanceVariable for a dictionary of words. We create the dictionary in awakeFromNib using NSScanner to parse the word dictionary. We handle the edit event in textStorageDidProcessEditing: as before. We start with the editedRange; this is usually a single character, but it might represent an arbitrary amount of text if the user pasted text from the clipboard. We extend the edited range out to word boundaries, taking advantage of the fact that words are separated by whitespace. Typically the "area" range represents is a single word, but it might represent several lines of text if this was a paste event again. As a result we still loop over the range like before. 

If you set up and run this program you'll find your words change colors as you type them. The word will flash between red and black as you complete the word. For example, when typing "there": 't' is black, 'th' is black (it's in the dictionary file...), 'the' is black, 'ther' is red, 'there' is black. If you paste text in from a big text file or mail message the program will stop and colorize all of the new text. This is the disadvantage I mentioned in my previous entry. But, once all of your text is in the file, you can scroll to any part of the file and start typing without feeling any negative effects of the colorizer. That is, even when you edit the middle of the file you don't have to re-colorize the entire document. That's the advantage I mention in my previous entry, and that's the specific problem ThomasSempf mentions in his previous post above.

This example is fairly simple, but the concept is quite scalable. My perl editor uses a more sophisticated version of this same strategy. We start with the edited range, roll back to the previous colored area (it might be a word, a keyword, a comment, a string) and roll forward as necessary until the colorizer is finished changing colors. This way, adding a "x" to the file affects only the characters immediately surrounding the "x", adding a "#" affects the characters just prior (you may have put it in the middle of a word) and all the characters before the next newline, and adding a "'" will cause all the strings in the file to temporarily unbalance until you add a second "'". This is all powered by a simple lexical analyzer I wrote to recognize common formatting in perl, shell, and make scripts. keywords are detected using a dictionary approach similar to the one I outline above; since keywords can vary depending on script type (perl keywords are different than shell keywords) the editor keeps a number of keyword dictionaries around.

----

Well after all (before you posted) i implemented it a more c way.
I am just searching for brackets to color the text between them.
Here it is, if you are interested:

    - (void)textStorageDidProcessEditing:(NSNotification *)notification {
        NSTextStorage *textStorage = [notification object];
        NSColor *blue = [NSColor blueColor];
        NSRange editedArea, area, colorRange;
        NSString *string = [textStorage string];
        unsigned int length = [string length];
        int i, j;
        bool strFound = NO;
        
        area.location = 0;
        area.length = length;
    
        editedArea = [textStorage editedRange];
    
        for(i=editedArea.location; i >= 0 && area.length != 0; --i) {
            if( [string characterAtIndex:i] == '<' ) {
                int temp = NSMaxRange( editedArea );
                editedArea.location = i;
                editedArea.length = temp - editedArea.location;
                break;
            }
        }
    
        for(i=NSMaxRange( editedArea ); i < NSMaxRange( area) && area.length != 0; i++) {
            if( [string characterAtIndex:i] == '>' ) {
                editedArea.length = i - editedArea.location + 1;
                break;
            }
        }
        
        [textStorage removeAttribute:NSForegroundColorAttributeName
                               range:editedArea];
    
        for(i=editedArea.location; i < (NSMaxRange( editedArea )); i++) {
            if ( [string characterAtIndex:i] == '<' ) {
                colorRange.location = i;
                strFound = YES;
            }
            if ( [string characterAtIndex:i] == '>' && strFound == YES ) {
                colorRange.length = i - colorRange.location + 1;
                [textStorage addAttribute:NSForegroundColorAttributeName 
                                    value:blue
                                    range:colorRange];
                strFound = NO;    
            }
        }

----

You are on the right track for writing a more general parser. If you're looking for a standard delimiter, such as angle brackets or spaces, then the NSCharacterSet approach I mention above is quite good. But some jobs require you to look at each individual character, such as most recursive descent parsers.

If you find using characterAtIndex: is too slow, there are some sophisticated improving access to the unicode string underneath.

.----

I'm interested how it will work in a Document Based Application.

----

I think the correct way to implement syntax coloring would be to use temporary attributes (see NSLayoutManager.h). The following is taken from the docs: "Temporary attributes are used only for on-screen drawing and are not persistent in any way. NSTextView uses them to color misspelled words when continuous spellchecking is enabled."

----

This approach pre-dates the temporary attributes in NSLayoutManager. If you already have control of your text storage it doesn't seem too inconvenient.

Using temporary attributes in NSLayoutManager should simplify some operations (you shouldn't need to remove the color attribute from the edited area, like the examples do above) and you need worry less about your text storage system. The big question is, how "temporary" are these temporary attributes? Under what circumstances are these attributes removed/ignored, exactly?

I'll leave the NSLayoutManager example as an exercise ;-)

*See ImplementSyntaxHighlightingUsingTemporaryAttributes for more on this, though*

----

I have a working syntax highlighter of sorts, based on the code above. The problem is taking care of elements of the language which span more than one word, for instance comments and strings.

I have a very shaky implementation which spots single line comments, but I'm at a loss when it comes to knowing whether what I am highlighting is in a multi-line comment or string (Which could also be multi-line)

----

I described using a lexical analyzer to interpret perl code. Perl is fairly easy, since you don't need to parse the code completely; you can just look for the things you're interested in (comments, strings, keywords). 

For performance I decided to write this part of my editor in C operating on a char *. I didn't want to spend all my time in NSString's characterAtIndex:, API like regex(3) only works on char *, and so forth. I end up breaking the file into "tokens" by scanning through the text looking for interesting data: comments, strings, keywords, and none-of-the-above. Sample flow control looks like this:

    next token()
        read character
        if character is a " or '?
          read character until we find matching character
            (be careful to ignore things like \")
          return string token 
        if character is a #
          read character until we find '\n'
          return comment token
        if ...

You can imagine more sophisticated state machines that can do a better job of parsing text, depending on the kind of thing you are parsing. For example, if you are processing things like HTML, XML, or old-style property lists you may need a recursive descent parser in order to keep all your tags balanced. You can also use tools like lex and yacc to build these kinds of parsers for you.

----

I've got the code above to work (ThomasSempf's), but it won't work when I replace the '<' and '>' with '"' (that's a double quote inside single quotes). All it does is color the quotes blue, not the text inside them.

*Add an else to the code. The current version works because if the current character is the start tag '<', it cannot be the end tag '>'. If you make them both identical, it will assume the start tag **is** the end tag, and just colour it, nothing else, blue. Put an else in there somewhere, and you should be fine:*

    for(i=editedArea.location; i < (NSMaxRange( editedArea )); i++) {
        if ( [string characterAtIndex:i] == '"' ) {
            if (strFound == NO) {
                colorRange.location = i;
                strFound = YES;
            } else {
                colorRange.length = i - colorRange.location + 1;
                [textStorage addAttribute:NSForegroundColorAttributeName 
                                    value:blue
                                    range:colorRange];
                strFound = NO;
            }
        }
    }

----

Given the current cooperation between the KDE folk & Apple, perhaps someone should look at porting the core of Quanta 3.1 across to Mac OS X?

I guess for full featured-ness, we need to wait for the WebCore SDK - in the meantime, give Quanta a whirl under X11 - it is a fantastic web editor!

----

Here is the source code for an editor I wrote, NSDocument based, but single window, open files are handled by a drawer. It does syntax highlighting by an external highlighter object which highlights a NSTextContainer.
http://ozy.student.utwente.nl/projects/ozyedit/

----

How's your speed on large documents? Unless I'm mistaken, you're rehighlighting the entire document each time, which can be very slow.

----

As you can see it uses some sort of caching approach to prevent it from rehighlighting it all. This was one of the design decisions: I want to extend the highlighter to be a syntax checker as well, highlighting your syntax errors while you type. Brackets will always match.

----

I've looked over the CodeEditor source and it will start running from a PB Project, but hangs. I was looking over the GNUstep site and they mention CodeEditor http://www.gnustep.org/experience/apps.html at the bottom of the page with the broken link you gave us above.  In looking over the ProjectCenter app though, the CodeEditor piece seems to be gone.

----

Here is yet another version. It works pretty well, but it isn't perfect. Please post any updates here so we can all benefit from the bug fixes.

    - (void)textStorageDidProcessEditing:(NSNotification *)notification
    {
        NSTextStorage *textStorage = [notification object];
        NSRange found, area;
        NSString *string = [textStorage string];
        unsigned int length = [string length];
        NSArray * keys = [keyWords allKeys];
        NSString * keyWord = nil;
        NSScanner * scanner = [NSScanner scannerWithString: string];
        int last_location = 0;
        NSString * searchString = nil;
        NSEnumerator * wordEnumerator;
        NSRange fooRange;
    
        area.location = 0;
        area.length = length;
        [textStorage removeAttribute: NSForegroundColorAttributeName range: area];
    
        // First colorize everything that is not enclosed in quotation marks
        while (![scanner isAtEnd]) {
    
            // Remember the last location of the scanner (initially zero, then the location of the last quotation mark)
            last_location = [scanner scanLocation];
    
            // Now scan up to the next (or first) quotation mark into a temporary string
            [scanner scanUpToString: @"\"" intoString: &searchString];
    
            // Get the length of the temporary string for use below
            length = [searchString length];
    
            // Advance the scanner one character past the quotation mark we just found
            // UNLESS the scanner is already at the end of the string
            if ([scanner scanLocation] < [string length]) {
                [scanner setScanLocation: [scanner scanLocation] + 1];
            }
    
            // Initialize the key word enumerator
            wordEnumerator = [keys objectEnumerator];
    
            // Loop through all of the key words in the array and try to find them within the temporary string
            while (keyWord = [wordEnumerator nextObject]) {
    
                // Initialize search range
                area.location = 0;
                area.length = length;
    
                // While there is a area to search...
                while (area.length) {
    
                    // Try to find a key word
                    found = [searchString rangeOfString: keyWord options: NSLiteralSearch range: area];
    
                    // If no key word found, try the next key word
                    if (found.location == NSNotFound) break;
    
                    // Otherwise a key word was found, lets go colorize it
    
                    // Setup a temporary range to reflect the actual position of the string within the text view
                    // by applying the offset of our current scanning location to the location of the key word
                    fooRange.location = found.location;
                    fooRange.length = found.length;
                    fooRange.location += last_location;
    
                    // Work around for an odd bug that only occurs occasionally
                    if (!string substringWithRange: fooRange] isEqual: keyWord]) {
                        fooRange.location++;
                    }
    
                    // Set the foreground color attribute for the range of text described by the key word
                    [textStorage addAttribute: [[NSForegroundColorAttributeName value: [keyWords objectForKey: keyWord] range: fooRange];
    
                    // Reduce the area to search to exclude the key word we just colorized
                    area.location = NSMaxRange(found);
                    area.length = length - area.location;
                }
            }
        }
    
        // Now reset the colorization for anything that was commented out
    
        // Note: This may seem like a hack, but I found that the performance of the app was
        // worse when trying to embed the logic for exclusion within the key word search.
    
        // Re-initialize a new scanner to scan the whole string
        scanner = [NSScanner scannerWithString: string];
        while (![scanner isAtEnd]) {
    
            // Try to find on open comment string
            [scanner scanUpToString: @"/*" intoString: nil];
            last_location = [scanner scanLocation];
    
            // If we can't find the closing comment string, then set the range to include the whole string
            if (![scanner scanUpToString: @"*/" intoString: nil]) {
                [scanner setScanLocation: [string length]];
            } else {
                // Otherwise we found the closing comment, so compute the range of text to reset
                NSRange resetRange;
    
                // Advance the scanner past the closing comment string we just found
                if ([scanner scanLocation] < [string length]) {
                    [scanner setScanLocation: [scanner scanLocation] + 2];
                }
    
                // Calculate the range to be reset
                resetRange = NSMakeRange(last_location, [scanner scanLocation] - last_location);
    
                // Reset the foreground color for the range
                [textStorage removeAttribute: NSForegroundColorAttributeName range: resetRange];
            }
        }
    
        // Now reset the single-line comments
    
        scanner = [NSScanner scannerWithString: string];
        while (![scanner isAtEnd]) {
            [scanner scanUpToString: @"//" intoString: nil];
            last_location = [scanner scanLocation];
            if (![scanner scanUpToString: @"\n" intoString: nil]) {
                [scanner setScanLocation: [string length]];
            } else {
                NSRange resetRange;
                if ([scanner scanLocation] < [string length]) {
                    [scanner setScanLocation: [scanner scanLocation] + 1];
                }
                resetRange = NSMakeRange(last_location, [scanner scanLocation] - last_location);
                [textStorage removeAttribute: NSForegroundColorAttributeName range: resetRange];
            }
        }
    }

You'll need to prepare an NSDictionary called "keyWords" that contains the color to use for each keyword. The keys are all of the key words and the values are the colors (NSColor objects) to set the text color. You can build an NSDictionary containing some key words like this:

    .
    .
    .
    NSMutableDictionary * keyWords = [[NSMutableDictionary alloc] init];
    NSColor * blue = [NSColor blueColor];
    NSColor * green = [NSColor colorWithCalibratedRed: 0.0 green: 0.5 blue: 0.0 alpha: 1.0];
    .
    .
    .
    [keyWords setObject: green forKey: @"forest"];
    [keyWords setObject: green forKey: @"grass"];
    [keyWords setObject: blue forKey: @"ocean"];
    [keyWords setObject: blue forKey: @"lake"];
    .
    .
    .

----

CodeEditor,  a fairly complete syntax coloring editor written for GnuStep has a new home:
http://savannah.nongnu.org/projects/codeeditor

----

FCBlogEditor has a neat implementation of syntax highlighting using flex.  http://members.shaw.ca/akochoi/2003/11-09/index.html#1

Some modifications to highlight everything inside of <> brackets and inside of quotation marks. This code is working fine:

    // Now colorize everything inside both brackets AND quotes
    
    scanner = [NSScanner scannerWithString: string];
    while (![scanner isAtEnd]) {
        
        // Try to find on open comment string
        [scanner scanUpToString: [keyWords objectForKey: @"quote-open"] intoString: nil];
        last_location = [scanner scanLocation];
        [scanner setScanLocation: [scanner scanLocation] + keyWords objectForKey: @"quote-open"] length;
                
        // If we can't find the closing comment string, then set the range to include the whole string
        if (![scanner scanUpToString: [keyWords objectForKey: @"quote-close"] intoString: nil]) {
            [scanner setScanLocation: [string length]];
        } else {
            // Otherwise we found the closing comment, so compute the range of text to colorize
            NSRange resetRange;
            
            // Advance the scanner past the closing comment string we just found
            if ([scanner scanLocation] < [string length]) {
                [scanner setScanLocation: [scanner scanLocation] + keyWords objectForKey: @"quote-close"] length;
            }
            
            // Calculate the range to be colorized
            resetRange = NSMakeRange(last_location, [scanner scanLocation] - last_location);
            
            if([textStorage attribute: NSForegroundColorAttributeName atIndex: resetRange.location effectiveRange: nil] == [keyWords objectForKey: @"bracket-color"]){
            // Set the foreground color for the range
            [textStorage addAttribute: NSForegroundColorAttributeName value: [keyWords objectForKey: @"quote-color"] range: resetRange];
            }
        }
    }

In the above code, "quote-open", "quote-close", etc, are defined in an NSDictionary as the beginning and ending symbols of a quote (currently just " "). Like I said, the code works fine, but every single keystroke raises an exception in the run log which reads:

    
Exception raised during posting of notification.  Ignored.  exception: *** -[NSConcreteScanner setScanLocation:]: Range or index out of bounds


I have narrowed it down to this line:
    
[scanner setScanLocation: [scanner scanLocation] + keyWords objectForKey: @"quote-open"] length;

but I cannot figure out any way to make the exception go away. The code is there to compensate for the scanner thinking that the beginning quote is also the end quote. Does anyone see any way that I could fix this?

I resolved the issue by using [scanner scanString] instead of [scanner setScanLocation].

----

You should always bracket changes in a text storage between -[textStorage beginEditing] and [textStorage endEditing] (defined in NSMutableAttributedString super class). This may significantly improve your colorizer performance.

----

    - beginEditing and     - endEditing are actually defined in NSMutableAttributedString (I changed your text accordingly).  You also might want to make sure that you disable and re-enable undo registration if you do call     - (begin|end)Editing.

----

I've written a nice class called UKSyntaxColoredTextDocument that demonstrates a fairly efficient approach at syntax coloring, where generally only what you actually changed gets recolored. Very useful for larger documents. See http://www.zathras.de/angelweb/sourcecode.htm#UKSyntaxColoredTextDocument. -- UliKusterer

----

Moved discussion of using temporary attributes for coloring syntax to ImplementSyntaxHighlightingUsingTemporaryAttributes

