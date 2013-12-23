---
layout: page
title: RTFOrWordDocsWithImages
---

Hi,

Does anybody know of any code out there that would allow me to export to RTF or .doc format from a text view/attributed string in such a way that images, tables and attachments are preserved?

Currently, -docFormatFromRange:documentAttributes:, -RTFFromRange:documentAttributes, and Tiger's new -dataFromRange:documentAttributes:error all cause the loss of images etc on export.

Yes, I know that I can always export to RTFD, but my program really needs to export to a format that Microsoft Word recognises (because users will most likely be sending exported files to Windows users), and sadly, MS Word - even on OS X - does not recognise RTFD files.

Both RTF and .doc formats _do_ support inline images, but sadly, Apple have yet to improve their own export methods (such as those mentioned above) so that they are fully compatible with these formats.

Any ideas?

Thanks!
KB

----

How about HTML? As of 10.4, NSAttributedString can write HTML (using the     dataFromRange:... or     fileWrapperFromRange:... methods). If you use the file wrapper method, it *might* be smart enough to save the embedded images as separate files in the file wrapper, with relative paths from the actual document. When you writet he file wrapper to disk, you'd get a directory and all of the appropriate files inside. I don't know if it really will save the images, but it's worth a try.

----

Thanks for the reply. I tried this, but it didn't work either. However (he says, months later), I have found a solution, and it is rather simple. I spent some time looking at the RTF specs here:

http://www.biblioscape.com/rtf15_spec.htm

(The relevant part is the "Pictures" section, obviously.) I also had a look inside a NIsus RTF file in BBEdit (because Nisus RTFs do support images). I finally figured out that the way to do it was simply insert the relevant RTF formatting code into a saved RTF myself, like this:


* Make a mutable copy of the text storage and parse through it looking for image attachments. Whenever you find one, save it in a dictionary with a unique key string and replace the image attachment in the text with this unique key string.
* Once you've removed all images from the text and stored them in your dictionary, turn the text into RTF data as normal, using -RTFFromRange:documentAttributes:.
* Load the RTF data into a normal NSString using NSASCIIStringEncoding, so that you get all of the RTF tags and everything in there.
* Parse through that string looking for the unique key strings you inserted. When you come across one, use it to get the image from your dictionary. You need to convert the image to a hexadecimal string in either PNG, JPG or BMP format. Some nice person has already posted code for an NSData extension with a hexadecimalRepresentation method on CocoaDev (see the MDFive page), so all you need to do is convert your image to data and then call this.
* Replace the key string in the RTF string with:


    

[NSString stringWithFormat:@"{\\*\\shppict {\\pict \\jpegblip %@}}", hexString]




(Note that you can replace "jpegblip" with "pngblip" for PNG data.)
* Finally, save the string to file as RTF (again using ASCII encoding).


The RTF file will then open in Word, Nisus or Mellel with images intact. Frankly, I have no idea why Apple haven't already added this to their existing RTF methods... Anyway, when I've got all my code tidied up, I will hopefully post an NSAttributedString category here that does all this for anyone who cares. :)
KB

----
Thanks for the psudo code KB, very helpful. I wrote up the function for a category.

BW
    
- (NSString *)encodeRTFwithPictures
{
	NSMutableDictionary *attachmentDictionary = [NSMutableDictionary dictionaryWithCapacity:5];
	NSMutableAttributedString *stringToEncode = [[NSMutableAttributedString alloc] initWithAttributedString:self];
	
	NSRange strRange = NSMakeRange(0, [stringToEncode length]);
	while (strRange.length > 0) {
		NSRange effectiveRange;
		id attr = [stringToEncode attribute:NSAttachmentAttributeName atIndex:strRange.location effectiveRange:&effectiveRange];
		strRange = NSMakeRange(NSMaxRange(effectiveRange), NSMaxRange(strRange) - NSMaxRange(effectiveRange));
		
		if(attr){
			//if we find a text attachment, check to see if it's one of ours
			NSTextAttachment *attachment = (NSTextAttachment *)attr;
			NSFileWrapper *fileWrapper = [attachment fileWrapper];
			NSImage *image = [[[NSImage alloc] initWithData:[fileWrapper regularFileContents]] autorelease];
			
			NSString *imageKey = [NSString stringWithFormat:@"Image#%i",[image hash]];
			[attachmentDictionary setObject:image forKey:imageKey];
			[stringToEncode removeAttribute:NSAttachmentAttributeName range:effectiveRange];
			[stringToEncode replaceCharactersInRange:effectiveRange withString:imageKey];
			strRange.length+=[imageKey length]-1;
		}
	}
	
	NSData *rtfData = [stringToEncode RTFFromRange:NSMakeRange(0,[stringToEncode length]) documentAttributes:nil];
	NSMutableString *rtfString = [[NSMutableString alloc] initWithData:rtfData encoding:NSASCIIStringEncoding];
	
	NSEnumerator *imageKeyEnum = [attachmentDictionary keyEnumerator];
	NSString *key;
	while(key = [imageKeyEnum nextObject]){
		NSRange keyRange = [rtfString rangeOfString:key];
		if(keyRange.location!=NSNotFound){
			NSImage *img = [attachmentDictionary objectForKey:key];
			NSBitmapImageRep *bitmap = [[NSBitmapImageRep alloc] initWithData:[img TIFFRepresentation]];
			NSString *hexString = [self hexadecimalRepresentation:[bitmap representationUsingType:NSPNGFileType properties:nil]];
			//pngblip or jpegblip depending
			NSString *encodedImage = [NSString stringWithFormat:@"{\\*\\shppict {\\pict \\pngblip %@}}", hexString];
			[rtfString replaceCharactersInRange:keyRange withString:encodedImage];
		}
	}
	
	return rtfString;
}

static const char *const digits = "0123456789abcdef";

- (NSString*)hexadecimalRepresentation:(NSData *)data
{
	NSString *result = nil;
	size_t length = [data length];
	if (0 != length) {
		NSMutableData *temp = [NSMutableData dataWithLength:(length << 1)];
		if (temp) {
			const unsigned char *src = [data bytes];
			unsigned char *dst = [temp mutableBytes];
			if (src && dst) {
				while (length-- > 0) {
					*dst++ = digits[(*src >> 4) & 0x0f];
					*dst++ = digits[(*src++ & 0x0f)];
				}
				result = [[NSString alloc] initWithData:temp encoding:NSASCIIStringEncoding];
			}
		}
	}
	return (result) ? [result autorelease] : result;
}

