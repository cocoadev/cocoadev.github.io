---
layout: page
title: MaskedTextField
---

I have a requirement that could benefit greatly from a Masked Text Field Implementation. Basically it's a text field where the input characteristics are defined with a format string (definition) and are constrained by the definition. In the Windows world, it would be equivalent to a Masked Edit Control.

Has anyone out there know of an existing Cocoa implementation, or should I start looking to build one from scratch?

I know some may feel that I shouldn't be constraining user input, but the fields in which I need this have a visible representation that includes separator symbols that are not immediately available from the keyboard and would be required for proper context. It only makes sense for a masked input where the cursor jumped around those symbols for the user. All the use needs to do is punch numbers/letters. A custom NSFormatter does not seem to do this.

----

There isn't anything currently in the framework -- Apple seems to use sort of a masked text field in the System Preferences / Date & Time + Energy Saver / Schedule (maybe also other places), I'm not too fond of it though.

If you do make your own text field please remember that the user may want to use copy/paste with it, using backspace/delete should feel "natural", and replacing characters is often done by first inserting the new character and then erasing the old one, which it should preferably also handle.

I am curious, exactly what information must your user input, and which punctuation characters is it that it uses?

If you end up with a gadget where the user can type only the letters/digits and then it will "jump over" the punctuation marks, then is an option not to simply not show punctuation while editing the string? or perhaps show the "parsed" punctuation next to the text field (and thus let the user do the edit in a normal text field)?

--AllanOdgaard

----

It's a geodetic application. One area that I am looking to "clean up" is position (longitude/latitude) type input.

I support several different geodetic systems where the display/input format can vary depending on the current geodetic system which is governed by either the project, document, or the overridden by the user in the applications preferences. It goes far beyond (read "not always is") simple deg/min/sec input. Most do require degrees indication on the first part of the input to separate it from the remainder. Yes, I could spread it across a couple of controls, but I never felt that those solutions looked or felt professional. The input experience **must be** uninterrupted and fluid.

I currently use NSFormatter(s) for static formatting and it works quite well. I followed the "Cocoa Programming" examples (Ch. 11) and while they come quite close to what I want for a user *input* experience, the solution does not quite reach the level of polish that I am after, and it is not as self contained as I would like.

The ideal solution would be an NSTextField subclass so I could just change the custom class in IB and be done with it. I have many of these types of inputs and the position input is only one example. I essentially already have a solution in place to handle pastes and drops as I already take arbitrary position information in from many different sources and formats.

Any known examples of a NSTextField subclass that does something real? Something on the order of NSSecureTextField? I starting making my way through the documentation regarding that, and I am realizing (or the docs are making me feel) that, 1. it is not a trivial subclass, and 2. the documentation seems vague on several key points. ;-)

----

You don't need to subclass NSTextField to implement a masked edit control, you can just use an NSFormatter, although it would be something of an understatement to say that the way to do it isn't obvious. To implement the masking behaviour, you need to override -isPartialStringValid:proposedSelectedRange:originalString:originalSelectedRange:errorDescription:. Unfortunately things get a bit complicated because of the need to make Delete and Backspace (aka Delete and Backwards Delete) work properly, and you also need to be careful to test copy and paste, both with the keyboard and the mouse pointer (it's easy to write code that will work properly for one, but not the other).

Here's some code that implements a masking formatter for a registration key (6 groups of 4 characters each, separated by '-' characters), and handles delete, backspace and paste in a reasonable way.

    
- (BOOL)isPartialStringValid:(NSString **)partialStringPtr
       proposedSelectedRange:(NSRangePointer)proposedSelRangePtr
	      originalString:(NSString *)origString
       originalSelectedRange:(NSRange)origSelRange
	    errorDescription:(NSString **)error
{
  NSCharacterSet *validChars = [NSCharacterSet
    characterSetWithCharactersInString:@"abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"];
  NSCharacterSet *validCharsPlusDelimiters
    = [NSCharacterSet characterSetWithCharactersInString:
      @"-abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"];
  NSCharacterSet *delimiters
    = [NSCharacterSet characterSetWithCharactersInString:@"-"];
  NSCharacterSet *invalidChars = [validCharsPlusDelimiters invertedSet];
  unsigned lengthOfPartial = [*partialStringPtr length];
  NSString *insertedString;
  NSScanner *scanner;
  unsigned proposedLocation = proposedSelRangePtr->location;
  NSMutableString *result;
  unsigned n;
  BOOL deleting = NO;
  BOOL deleteExtraChar = NO;
  unsigned extraCharLocation = 0;
  NSEvent *curEvent = [NSApp currentEvent];
  NSEventType curEventType = [curEvent type];
  
  UNUSED (origString);
  
  /* Empty strings are valid */
  if (!lengthOfPartial)
    return YES;

  if (curEventType == NSKeyDown || curEventType == NSKeyUp) {
    /* HACK: in order to detect forward delete, we need to examine the 
      current event. */
    if ([[[NSApp currentEvent] characters] characterAtIndex:0]
	== NSDeleteCharacter) {
      deleting = YES;
    }
  }
  
  /* Try to figure-out what was inserted, if anything */ 
  if (proposedSelRangePtr->location <= origSelRange.location) {
    NSRange oRange;
    
    /* Characters were cut or deleted */
    insertedString = @"";
    
    /* HACK: in order to detect the difference between single-character
       delete/forward-delete operations, we need to retrieve the real
       selected range from the field editor. */
    oRange = [(NSText *)[[NSApp keyWindow] firstResponder] selectedRange];
        
    /* For single insert or delete operations across a delimiter,
       apply the operation to the adjacent character as well. */
    if (oRange.length == 0
	&& proposedSelRangePtr->location >= 4
	&& ((proposedSelRangePtr->location - 4) % 5) == 0) {
      deleteExtraChar = YES;
      if (!deleting) {
	/* Delete key */
	extraCharLocation = proposedSelRangePtr->location;
      } else {
	/* Backspace key */
	extraCharLocation = proposedSelRangePtr->location - 1;
	--proposedLocation;
      }
      
      extraCharLocation -= (extraCharLocation / 5);
    }
  } else {
    insertedString = [*partialStringPtr substringWithRange:
      NSMakeRange (origSelRange.location,
		   proposedSelRangePtr->location - origSelRange.location)];
    
    /* Make sure there are no invalid characters */
    if ([insertedString rangeOfCharacterFromSet:invalidChars].location
	!= NSNotFound) {
	if (error)
	  *error = NSLocalizedString (@"Invalid characters in registration key",
				      @"Invalid characters in registration key");
      return NO;
    }
  }
  
  /* Create a new mutable string */
  result = [NSMutableString string];
  
  /* Create and initialise a scanner */
  scanner = [NSScanner scannerWithString:*partialStringPtr];
  [scanner setCharactersToBeSkipped:
    [NSCharacterSet characterSetWithCharactersInString:@""]];
  
  while (![scanner isAtEnd]) {
    NSString *scanned;
    
    /* Discard any delimiters */
    if ([scanner scanCharactersFromSet:delimiters intoString:&scanned]) {
      if ([scanner scanLocation] <= proposedSelRangePtr->location)
	proposedLocation -= [scanned length];
      else if ([scanner scanLocation] - [scanned length]
	       <= proposedSelRangePtr->location)
	proposedLocation -= ([scanned length] 
			     - ([scanner scanLocation]
				- proposedSelRangePtr->location));
    }
    
    /* Accumulate characters */
    if ([scanner scanCharactersFromSet:validChars intoString:&scanned])
      [result appendString:[scanned uppercaseString]];
  }
  
  if (deleteExtraChar)
    [result deleteCharactersInRange:NSMakeRange (extraCharLocation, 1)];
  
  if ([result length] > 4 * 7) {
    if (error)
      *error = NSLocalizedString (@"Registration key too long",
				  @"Registration key too long");
    return NO;
  }
  
  for (n = 0; n < 6; ++n) {
    unsigned pos = 4 + 5 * n;
    
    if (pos <= [result length]) {
      [result insertString:@"-" atIndex:pos];
    
      if (pos <= proposedLocation && !(deleting && proposedLocation == pos))
	++proposedLocation;
    }
  }
  
  *partialStringPtr = result;
  *proposedSelRangePtr = NSMakeRange (proposedLocation, 0);
  if (error)
    *error = nil;
  return NO;
}
@end


There are a few things to note about this code; first, there is an unpleasant hack necessary in order to distinguish the Delete and Backspace cases from within the formatter. This isn't ideal, but I don't think it's likely to break at any point in the future. It also isn't as efficient as it might be (it could cache those NSCharacterSet objects, for instance). Also, note that if you change the string, you will need to adjust the location of the cursor or selection accordingly - otherwise your text field will be very confusing for the user.

Now, I wouldn't recommend using masked text fields in general; >95% of the time, they are unnecessary and confusing. But for some specialized applications, like entering a registration key, they are potentially very useful indeed.

--Alastair.

----

I did look at coding a custom NSFormatter for this when I started. It worked great for display.

I would feel that my needs do fall in that upper 5% where it does need to be masked input.

1. I want to drag these on a window in IB (or as a table cell) and forget about them.

2. The user can choose one of several formats for display and input.

3. All of the currently visible entries should update automatically when these preferences change. Easily taken care of with a notification whether it be a custom NSTextField/NSTextFieldCell or NSFormatter.

4. There needs to be visible placeholders for the mask as to remind the user what format is current and to indicate the constraints of a given format. Let me explain this a little further.

A short list (far from complete) of common formats could go something like this...
    
DD� MM" SS'
DD� MM.mmm'
DD� MM.mmmm'
DDMM.mmm'
DD.dddd�

Which would input/display *right justified* as...
    
	Latitude Fields	Longitude Fields
	12� 11' 11" N	074� 32' 12" W
	12� 11.183' N	074� 32.200' W
	12� 11.1833' N	074� 32.2000' W
	1211.183' N	07432.200' W
	12.1863� N	074.5366� W

So ideally it would be presented to the user *right justified* like:
    
	Latitude Fields	Longitude Fields
	__� __' __" N	___� __' __" W
	__� __.___' N	___� __.___' W
	__� __.____' N	___� __.____' W
	____.___' N	_____.___' W
	__.____� N	___.____� W

I also allow (will allow) the North/South or East/West (or +/- depending on the users format) to be changed with the +/- keys or by pressing N, S in latitude or E, W in longitude fields.

Special input keys such as 'C' for current position will also be allowed.

I can handle the paste pretty easily as I already wrote methods that can grep an input and can convert it to whatever I want. I typically use WGS84 decimal degrees (doubles) internally.

So what do you think, custom NSFormatter or NSTextField/NSTextFieldCell (and friends) subclass.

----

I have to say I'm not 100% convinced that latitude/longitude fields are a case where I would use a masked edit field. It looks to me as if it should be easy enough to distinguish the different formats when reading them (although I don't know nearly enough about latitude and longitude formats to say for certain if that will always be true), which is usually a good indication that your fields should accept any format and display whatever the user's current preference is. This would also remove the problem of handling +, -, N, S, E and W specially.

As for handling 'C' to mean current position, the easiest thing is probably to scan for 'C' characters in your NSFormatter, which seems a bit hacky, but it should be quite straightforward. A cleaner (but more complicated) technique would be to subclass NSTextView and use your subclass to replace the field editor for the text fields you want to behave this way.

--Alastair

----
> *A cleaner (but more complicated) technique would be to subclass NSTextView and use your subclass to replace the field editor for the text fields you want to behave this way.*

I agree, but thats where I run into confusion. There doesn't seem to be allot of good explanation/samples on this subject, or I am just having a hard time getting my head around the NSTextField/Cell to NSTextView/FieldEditor interaction. I am kind of bent on the masked text fields because the � (degree Shift-Option-8) is not obvious and I really don't want to subject my users to having to supply that or any other graphic symbols during input. Within this applications target market (traditionally non Mac OS X) these inputs are normally handled with masked text fields, and my users are really going to expect them. They just want to punch the numbers and let the UI do the rest (jump around the symbols). No degree, minute, seconds, or decimal characters. It's just what they are used to.

----

It's easy to be overwhelmed by the Cocoa Text system (well, I think so, anyway); for the type of thing that you are considering, you probably want to read

  http://developer.apple.com/documentation/Cocoa/Conceptual/TextEditing/Tasks/FieldEditor.html

and in particular the section about using a custom field editor.

An alternative to the suggestion in that document of writing a -windowWillReturnFieldEditor:toObject: method on you window's delegate is to implement -setUpFieldEditorAttributes: on an NSTextFieldCell subclass to return your NSTextView subclass instead of the object it is passed. This has the advantage that it makes the new text field more self-contained (you don't need to alter the delegate objects for windows that contain your special text field cells), but has the disadvantage that you need to manually set-up the new cell classes for affected text fields and table columns.

As far as inputting degree signs, don't forget that you can easily accept a wide range of alternative symbols - "o", "deg" and "d" don't seem like bad choices, for instance. Also, if you're already going to change the behaviour of the 'C' key, then changing 'O' so it generates a degree symbol isn't exactly difficult.

--Alastair.

----

If your user interface needs are more complex than an NSFormatter can handle, I'd suggest making your own NSView subclass. The bread-and-butter of a text field, displaying text, is a relatively simple task. Then, you can implement the various NSResponder methods for handling keyboard input to do the keystroke processing you want. Do copy-and-paste however you want to. Don't allow users to click on the string and edit at random points, etc.

----

The NSTextField, NSTextFieldCell and NSTextView (Field Editor) subclasses is the direction I have started down (again) but the documentation on all this, while a good start, does not seem to take it far enough. It seems to stop short of giving me the pieces I need to keep this as self contained as say implementing something along the lines of an NSSecureTextField look-a-like, which is about what I need to create what I have been describing. I really just want to make IB aware of the NSTextField and NSTextFieldCell subclasses and change the NSTextField/Cell custom class of the fields/cells of interest.  I really don't want to subclass NSView directly as it breaks my concepts of OOP.

----

NSDatePicker creates the exact desired effect. Thing is it works only for date & time. I would love to see a similar implementation for degrees-minutes-seconds. I just have no idea where to start. This seems to be quite a complex cell to write.

