---
layout: page
title: WriteAFileWithAccent
---

I developped an little piece of software called General/MeNotes (you can download it on www.bougnatware.com) that let's you manage little personnal notes. One of the feature it's to export a note to a text file, for example to put it on an iPod.

But I have problem with accent (�,�,etc.) that don't be displayed correctly. I try to fix this bug for a long time, without success : i begin to be desesperate ! :-)

My function :
    
- (General/IBAction)exportToIpod:(id)sender
{
	General/NSSavePanel *General/MySavePanel;
	int runResult;
	General/NSString *noteAExporter;
	General/NSData *texte;
	
	// Create the message to export
	noteAExporter = @"Titre : ";
	noteAExporter = [noteAExporter stringByAppendingString:[titleField stringValue]];
	noteAExporter = [noteAExporter stringByAppendingString:@"\nDate : "];
	noteAExporter = [noteAExporter stringByAppendingString:[dateField stringValue]];
	noteAExporter = [noteAExporter stringByAppendingString:@"\nContenu : "];
	noteAExporter = [noteAExporter stringByAppendingString:[noteField string]];
	
	texte = [noteAExporter dataUsingEncoding:NSUTF8StringEncoding];
 
	// Create a General/SavePanel
	General/MySavePanel = General/[NSSavePanel savePanel];
 
	General/[MySavePanel setRequiredFileType:@"txt"];
 
	// Display the Save Panel
	runResult = General/[MySavePanel runModalForDirectory:General/NSHomeDirectory() file:@""];
	
       // Write the file
	if (runResult == General/NSOKButton) {
		if (![texte writeToFile:General/[MySavePanel filename] atomically:YES])
			General/NSBeep();
	}
}


It doesn't work... When I open the file with Smultron, no accent, "barbarian sign" :-)

For Smultron, the file is encoded in ISO Latin 1.

Thanks for idea,
JB

----
Smultron thinks it's ISO Latin 1, you're using UTF-8. Why it doesn't work should be obvious, as should the fix.

----
I'm sorry but I think and think again, and I don't understand what is obvious... Is the NSUTF8StringEncoding argument not correct ? But what is the correct one ? Or Smulton is buggued ?

Maybe I wasn't clear in my explication : when i open a file write by my app with Smultron, it "thinks" that is ISO Latin 1. For it work, the file need to be in UTF8, isn't it ?

Could you help me more please ?

----
Either you need to convince Smultron that the file is UTF-8, or you need to change your code to write Latin1.

----
When I convince Smultron that the file is UTF-8, it's ok, the accent is displayed. But how can I do for Smultron/General/TextEdit recognize the encoding itself ? Because I can't say to my users to change the encoding each time they open a file in their favourite text editor. And this idea doesn't work on an iPod : the iPod doesn't display the accent whereas the iPod display the accent when it's a file in UTF generated by Smultron...

----
You might try writing out a BOM. Otherwise there is no way; pure text files have no way to indicate their encoding, the reading application either just has to guess or has to know through other means, such as the user telling it.

----
I'm so happy because I fix the problem ! Without write a BOM...

    
- (General/IBAction)exportToIpod:(id)sender
{
	General/NSSavePanel *General/MySavePanel;
	int runResult;
	General/NSString *noteAExporter;
	
	// Create the message to export
	noteAExporter = @"Titre : ";
	noteAExporter = [noteAExporter stringByAppendingString:[titleField stringValue]];
	noteAExporter = [noteAExporter stringByAppendingString:@"\nDate : "];
	noteAExporter = [noteAExporter stringByAppendingString:[dateField stringValue]];
	noteAExporter = [noteAExporter stringByAppendingString:@"\nContenu : "];
	noteAExporter = [noteAExporter stringByAppendingString:[noteField string]];
 
	// Create a General/SavePanel
	General/MySavePanel = General/[NSSavePanel savePanel];
 
	General/[MySavePanel setRequiredFileType:@"txt"];
 
	// Display the Save Panel
	runResult = General/[MySavePanel runModalForDirectory:General/NSHomeDirectory() file:@""];
	
       // Write the file
	if (runResult == General/NSOKButton) {
		if (![noteAExporter writeToFile:General/[MySavePanel filename] atomically:YES])
			General/NSBeep();
	}
}


I directly write my General/NSString in the file, without use DATA type, and without specify encoding. And it work on iPod (the most important for me and my users) and with General/TextEdit (the most used editor on Mac). In Smultron, the accentuated caracter disappear ! Strange but it's not really important : what I wanted is to export for iPod !

Thank a lot.

----

The issue is that it's almost impossible to detect automatically between Latin 1 and UTF-8 since both are 1-byte formats and are essentially data. You could look at an extensive document and say statistically that there are no illegal UTF-8 sequences, and thus that (assuming there's a more or less random distribution of data, which there normally isn't) the file is _probably_ UTF-8. Or you could take the unassigned characters above 128 in Latin 1 and say that if you see some in the document, then it _isn't_ Latin 1 (but what is it? UTF-8? who knows!)

The only you can really distinguish between those encodings better is when you have text that exhibits many characters from above the 128 range of ASCII. This is because both UTF-8 and Latin 1 (and several others) are identical to ASCII for this lower range, and are thus indistinguishable from one another. Your addition of one accented character does not give your editor enough information to infer that you're probably using Latin 1 or anything else.

The most flexible fix for you would simply be to resave your file as UTF-8 and maintain your original code. With UTF-8 you can represent just about any character ever imagined. You can configure smultron to open and save UTF-8 by default, but you need to explicitly save your file as UTF-8 before that will work, or the accent in your file will be lost.

Hope this clears things up,
-General/DanielPeebles

----
It would pay to read this page: http://www.joelonsoftware.com/articles/Unicode.html