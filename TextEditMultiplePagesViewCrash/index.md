---
layout: page
title: TextEditMultiplePagesViewCrash
---

I'm using the General/MultiplePageView class from General/TextEdit to paginate and print text pages. It works great except for when you try to include an attachment that's taller then the text container. In that case the app (both mine and General/TextEdit) goes into a loop and freezes. You can see the problem by pasting a tall (taller then a page) image into General/TextEdit and then choose Format > Wrap To Page, that should crash the app every time. It appears that the text layout machinery doesn't know what to do when a single character won't fit within a text container and it goes into an infinite loop calling this delegate method:

- (void)layoutManager:(General/NSLayoutManager *)layoutManager didCompleteLayoutForTextContainer:(General/NSTextContainer *)textContainer atEnd:(BOOL)layoutFinishedFlag {

but never finishing.

Can anyone suggest a workaround?

Thanks,
Jesse

----
Is the General/NSTextContainer sent to the delagate nil? That's how the layout manager lets the delegate know there wasn't enough room... 

*A suggestion: In case you don't have it, get a copy of General/AppKiDo. It's very helpful when dealing with problems like this.*

----
Did you mean General/NSLayoutManger? I don't think General/NSTextContainer has a delegate. In any-case I do have a delegate set on General/NSLayoutManager. The problems is that the delegate method:

- (void)layoutManager:(General/NSLayoutManager *)layoutManager didCompleteLayoutForTextContainer:(General/NSTextContainer *)textContainer atEnd:(BOOL)layoutFinishedFlag {

Keeps getting called repeatedly with layoutFinishedFlag set to false. The problem is I'm not sure how to handle this case and give it more space. It doesn't really make sense to change the size of the text container since that's supposed to match the paper size.

----
It's the layout manager's delegate. What I was referring to is the     didCompleteLayoutForTextContainer:(General/NSTextContainer *)textContainer part of the     layoutManager message sent to the delegate. If     textContainer is nil then you know there wasn't enough room. You'd either have to add more room or, in the case of an image that is just too big for the page, scale down the image to fit the page. Apparently, the layout manager is a persistent bugger... it won't give up until it has the right amount of space. Maybe it would be better to scale oversize images to fit before handing them over to the layout manager in the first place?

----

It turns out that the problem isn't just related to text attachments. It will happen anytime there is a single glyph that's to large to fit in a text container. Here's the solution that I came up with. Grow the text container to fit the glyph that won't fit. This works OK for most cases. But it still doesn't work perfectly for some text attachment types such as multipage PDF attachments. If anyone has improvements or a better way to solve this please post here. 

    
- (void)layoutManager:(General/NSLayoutManager *)layoutManager didCompleteLayoutForTextContainer:(General/NSTextContainer *)textContainer atEnd:(BOOL)layoutFinishedFlag {
    General/NSArray *containers = [layoutManager textContainers];
    
    if (!layoutFinishedFlag || (textContainer == nil)) {
	// Either layout is not finished or it is but there are glyphs laid nowhere.
	General/NSTextContainer *lastContainer = [containers lastObject];
	
	if ((textContainer == lastContainer) || (textContainer == nil)) {
	    General/NSRange glyphRange = [layoutManager glyphRangeForTextContainer:textContainer];
	    
	    if (glyphRange.length == 0) {
		// Adjust text container size if textContainer doesn't contain any glyphs. 
		unsigned int characterIndex = [layoutManager characterIndexForGlyphAtIndex:glyphRange.location];
		General/NSAttributedString *attributedString = General/layoutManager textStorage] attributedSubstringFromRange:[[NSMakeRange(characterIndex, 1)];
		General/NSSize stringSize = [attributedString size];
		General/NSSize containerSize = [textContainer containerSize];
		
		if (containerSize.width < stringSize.width) containerSize.width = stringSize.width;
		if (containerSize.height < stringSize.height) containerSize.height = stringSize.height;
		
		[textContainer setContainerSize:containerSize];
	    } else {
		// Add a new page only if the newly full container is the last container or the nowhere container.
		[self addPage];
	    }
	}
    } else {
	// Layout is done and it all fit.  See if we can axe some pages.
	unsigned lastUsedContainerIndex = [containers indexOfObjectIdenticalTo:textContainer];
	unsigned numContainers = [containers count];
	while (++lastUsedContainerIndex < numContainers) {
	    [self removePage];
	}
    }
}

