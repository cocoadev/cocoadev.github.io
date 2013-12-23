---
layout: page
title: FormattedText
---

**A couple of basic questions/answers on text attributes, etc. - See also, NSFontPanel**

I am trying to put some formatted text in a NSTextView. Something like a report:

    
**A PERSON   **            
Name  Gender  DOB      
abc   m       030568         
abc   m       030568      
abc   m       030568  


I have got the NSAttributedString for above data, but don't know how to put them up  formatted. Is there a simple example?
   
Also, I need two parts of them SIDE BY SIDE on the same paragraph like this:

    
**A PERSON                  B PERSON**        
Name  Gender  DOB         Name Gender DOB 
abc   m       030568      cde  f      040271  
dcm   f       030568      mui  m      040271 
wmx   m       030568      oji  f      040271    
                          lmn  f      040271
                          dfd  f      040271




----

Why are you  not using a tableView? I think you can use printf tokens to get spacing. 

----

I have some other analysis results to be put into the same NSTextView, which are just statements, and do not fit into NSTableView. Also I need RTF formatting of the content later on, and do some fonts-switching job on the report.  I think NSTextView can handle those tasks.

----

Look into the printf method I suggested. Here is a link: http://www-ee.eng.hawaii.edu/Courses/EE150/Book/chap8/subsection2.1.2.1.html Im not sure if it will work, but I think it may have some usefulness. Still think a tableview would be better, and then use this formatting when it comes time to generate RTF.

----

One way to do this is to set the string first and then change the attributes.

    
[textView setString:@"BOLD red green"];
[textView setFont:[NSFont fontWithName:@"Helvetica-Bold" size:12] range:NSMakeRange(0,4)];
[textView setTextColor:[NSColor redColor] range:NSMakeRange(5,3)];
[textView setTextColor:[NSColor greenColor] range:NSMakeRange(9,5)];


You should be able to set an NSTextView with an attributed string though.

----

Use the NSTextView's NSTextStorage, which is a subclass of NSMutableAttributedString.  It looks like this:

    textView textStorage] setAttributedString:attributedString];

----

I have a simple app where any text that's selected from [[NSTextView in my window will be dumped into a nearby NSTextField in the same window, like so:

    
- (NSString *) selectedText

{   NSString	*result;

    result = [sourceText textStorage] string] substringWithRange: [sourceText selectedRange;
	
    return result;

        }
...

- (void)textViewDidChangeSelection:(NSNotification *)aNotification {
        NSString *selected = [[NSString alloc] init];

        selected = [self selectedText];

        if ([selected isEqualToString:@""]) {                 

        } else {

                [outputField setString:selected];
        }

}


How can I display the text with a specific attribute, like 14 point Arial, "on the fly", so to speak? 

I've tried reading documention, and I can't be sure if this is an NSText question, or an NSString question or an NSFont question.

Also: Just read some snippets, and now I gather that I need to add this to my header file:

    NSFont *someFont;

    [outputField setFont:[NSFont fontWithName: @"Arial" size:14]];

----

I noticed there are some chunks of code that could be removed, for the sake of clean coding.

For example, the "string" method of NSTextView always return raw text without any formatting, so you could cut to :

    result = sourceText string] substringWithRange: [sourceText selectedRange;

Your "if" statement could be reduced to :

    if ( ! [selected isEqualToString:@""] ) [outputField setString:selected];

Lastly, your string named "selected" may cause a memory leak if you don't release it.

