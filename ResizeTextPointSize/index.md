---
layout: page
title: ResizeTextPointSize
---

I wanted my text to scale with my NSTextView, so I tried to pull it off with the following methods. I came fairly close, and I think the resulting issues may be with the NSScrollView. If anyone has any suggestions on how to improve or increase the accuracy, it would be very much apreciated. 

Updated Oct. 12, 10:51PM -- Major speed boost

The more I try, the more ways I approach this, the more miserable I become. I don't think it is possible. Ill have to find another way to get similiar results. Chsnging monitor resolution wouldnt work either.

    
-(void)scaleText:(NSTextView *)txtView width:(float)width height:(float)height
{

int count = txtView string] length];

int i;

[[NSMutableDictionary *at;

NSSize original;

NSSize desired;

NSRange range;

at = [[NSMutableDictionary alloc] init];

for (i=0; i < count; i++)
    {
        
   [at setDictionary: txtView textStorage] attributesAtIndex:i effectiveRange:&range;  
         
       original = [txtView textStorage] attributedSubstringFromRange:range] size];
       
        desired = [[NSMakeSize(original.width*width,original.height*height);

txtView textStorage] beginEditing];

[[txtView textStorage] addAttribute:@"[[NSFont" value:[[NSFontManager sharedFontManager] convertFont: [at objectForKey:@"NSFont"] toSize: [self pointSizeForString:txtView textStorage] attributedSubstringFromRange:range] convertToSize:desired] ] range:range];

[[txtView textStorage] endEditing];

i = range.location + range.length;
    }

}

-(float)pointSizeForString:([[NSMutableAttributedString *)string convertToSize:(NSSize)newSize
{
NSSize originalSize;

NSSize testSize;

NSMutableDictionary *at;

at = [[NSMutableDictionary alloc] init];

 [at setDictionary: [string attributesAtIndex:0 effectiveRange:nil]];        

float originalPoint =at objectForKey:@"[[NSFont"] pointSize];

float xFactor;

float yFactor;

originalSize = [string size];

[string addAttribute:@"NSFont" value:[[NSFontManager sharedFontManager] convertFont: [at objectForKey:@"NSFont"] toSize: originalPoint+1 ] range:NSMakeRange(0,[string length])];

testSize = string string] sizeWithAttributes:[string attributesAtIndex:0 effectiveRange:nil;
 
xFactor = testSize.width - originalSize.width;

yFactor = testSize.height - originalSize.height;

[at release];

return (newSize.height/yFactor)*yFactor;

}

-- GormanChristian

