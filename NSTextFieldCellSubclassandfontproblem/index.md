---
layout: page
title: NSTextFieldCellSubclassandfontproblem
---

Describe NSTextFieldCellSubclassandfontproblem here.



Im developing a font managment software in cocoa, and im having problems with the preview of the fonts.

For the preview Im using a NSTableView, with a subclass of NSTextFieldCell. In this subclass I draw the name of the font and a text example with the font.

The problem is after I preview the font in a cell, i can�t deactivate it. It seems like the NSFont object used in the cell is not released and dont allow to deactivate the font.

The NSTextFieldCell Subclass code:

    

@implementation TextPreviewCell

- (void)dealloc {
	NSLog(@"deallocing CELL");
	[datosColumna release];
	datosColumna = nil;
        [super dealloc];
}//end method

- (void)drawWithFrame:(NSRect)cellFrame inView:(NSView *)controlView {
        
		NSRect	rectanguloCabecera;
		NSDivideRect(cellFrame, &rectanguloCabecera, &cellFrame, 14, NSMinYEdge);
		
		if (sStripeColor == nil) sStripeColor = [[NSColor colorWithCalibratedRed:STRIPE_RED green:STRIPE_GREEN blue:STRIPE_BLUE alpha:1.0] retain];
		[sStripeColor set];
		rectanguloCabecera.size.width = (rectanguloCabecera.size.width + 8);
		rectanguloCabecera.origin.x = (rectanguloCabecera.origin.x - 1);
		rectanguloCabecera.origin.y = (rectanguloCabecera.origin.y - 2);
   		NSRectFill(rectanguloCabecera);
		
		NSPoint p;
		p.x = rectanguloCabecera.origin.x + 3;
		p.y = rectanguloCabecera.origin.y + 1;
		
		int columna = [controlView rowAtPoint:p];
		NSString *nombreFuente = controlView dataSource] getDataRow:columna];
		
		[[NSFont *fuenteCabecera = [[NSFont fontWithName:@"LucidaGrande" size:10] autorelease];
		NSMutableDictionary *estilo = [NSMutableDictionary dictionary]; [estilo setObject:fuenteCabecera forKey:NSFontAttributeName];
		[nombreFuente drawAtPoint:p withAttributes:estilo];
		
		if (tamanoFuente == 0) [self setTamanoFuente:8];
		if (textoCelda == nil) [self setTexto:@"sin asignar"];
		
		NSFont *fuente = [[NSFont fontWithName:nombreFuente size:tamanoFuente] autorelease];
		
		[self setFuente:fuente];
		[self setFont:font];
		[self setStringValue:textoCelda];
		[self setLineBreakMode:NSLineBreakByTruncatingTail];
		[self setWraps:NO];
	
		[super drawWithFrame:cellFrame inView:controlView];

}



For activating the font Im using: 

    
err = ATSFontActivateFromFileSpecification(&fspec, kATSFontContextGlobal, kATSFontFormatUnspecified, NULL, kATSOptionFlagsDefault, &id_fuente);


And for deactivating: 

    
ATSFontDeactivate(id_fuente, NULL, kATSOptionFlagsDefault);


If I activate the font without preview in the table, works fine, activating and deactivating the font.

I note that if I put a NSLog in the dealloc method of my NSTextFieldCell subclass, it sends the log when i click in the row or when i move the scrolls, but it not log when I 
reload data in the data source of the NSTableView, but the rows are deleted and the new data is displayed. �are those cells deallocated?.

Sorry for my English and thanks.

C�sar Escolar

