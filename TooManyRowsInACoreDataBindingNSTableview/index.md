---
layout: page
title: TooManyRowsInACoreDataBindingNSTableview
---



I'm changing a non document based application to use core data, to gain some of core data's freebie advantages.
I appear to have got the core data part working okay. 

The Entity Graph looks like this.

Genome ---1 to N---> General/XForms ---1 to N--> Variations 

The data is build the hard way as the app is designed to be compatible with another application's file
format. So for example...

    

	genomeEntity = General/[NSEntityDescription insertNewObjectForEntityForName:@"Genome" inManagedObjectContext:moc];
	....

	xFormSet = General/[[NSMutableSet alloc] initWithCapacity:numberOfXForms];
	for(i=0; i<numberOfXForms;i++) {
		xFormEntity = General/[NSEntityDescription insertNewObjectForEntityForName:@"General/XForm" inManagedObjectContext:moc];
		[xFormSet addObject:xFormEntity];	
              ...
             /* similar loop for variations */
       }
	[genomeEntity setValue:xFormSet forKey:@"xforms"];




Which is how I interpret 'Manipulating Relationships' in the core data guide. I can add my 
data into a General/NSManagedObjectContext and get it back out again, so I guess that is fairly correct.

I've bound an General/NSArrayController to my managedObjectContext, with attributes set to the Genome entity.
I've got an General/NSTableView with Columns bound to two of the Genome attributes via the General/NSArrayController
arrangedObjects controller key.

When I run my Application I get a row in the table for each Variation entity in the context. 
If I comment out the addition of the variations code I get one for each General/XForm entity, and if
I comment out the General/XForm code I get what I was expecting in the first place, one level for each Genome
Entity. 

So How do I get just one row in the table for the top level entity without getting rid of all my code
or is that just the way things are supposed to work ?


----
This is not going to help you much, but at least, I know one thing for sure: you should have only 2 rows, one per Genome, if things are done properly, no matter how many 1--N relationships the entity has. I am not sure where you should look first, the code you give looks good. Maybe you can build a small test case for which you can show all the code? In  which methods are the xForms added (e..g in awakeFromInsert?)? Also, check your model: does any of your entity use a subclass of General/NSManagedObject for extra methods?  Finally, be more descriptive about the nib setup.

----
As the code is GPL I've put a vastly cut down copy available at http://oxidizer.sf.net/oxidizer_core.zip
I weighs in at a touch under 3MB.
All the following describes the cut down project....

The nib is as I described it above.

I have an object, General/FractalFlameModel, with an General/NSManagedObjectContext public variable 'moc'. The persistent store is an in memory store.
created in General/FractalFlameModel's init method.
I've bound an General/NSArrayController' managedObjectContext binding to 'moc', with attributes set to Entity, Genome.
I've got an General/NSTableView with the Columns value binding bound to two of the Genome attributes via the General/NSArrayController arrangedObjects controller key.
There's a controller class to that now does not do much controlling. And two windows, oxidizer, the main window and preferences which I would get rid of but
can not delete for some reason. 

The code that is used to populate the context is below createGenomeEntityFrom:withImage:inContext: is called in
a loop from the model class. The Genome class is a factory class hence the class methods.
...
    
@implementation Genome

+ (General/NSManagedObject *)createGenomeEntityFrom:(flam3_genome *)genome withImage:(General/NSImage *)image inContext:(General/NSManagedObjectContext *)moc {

	General/GenomeManagedObject *genomeEntity;

	/* create a genome entity */
	genomeEntity = General/[NSEntityDescription insertNewObjectForEntityForName:@"Genome" inManagedObjectContext:moc];

	[genomeEntity setValue:General/[NSNumber numberWithDouble:genome->time]  forKey:@"time"];
	[genomeEntity setValue:image forKey:@"image"];
	[genomeEntity setValue:[Genome createXFormEntitySetFromCGenome:genome inContext:moc] forKey:@"xforms"];

	[moc save:nil];

	return genomeEntity;
}

+ (General/NSMutableSet *)createXFormEntitySetFromCGenome:(flam3_genome *)genome inContext:(General/NSManagedObjectContext *)moc {

	General/NSManagedObject *xFormEntity;	
	General/NSMutableSet *xforms; 
	General/NSMutableSet *variations; 
	int i;
	flam3_xform *xform;

	/* create a General/XForm entity */
	xforms = General/[[NSMutableSet alloc] initWithCapacity:genome->num_xforms];
	
	for(i=0; i<genome->num_xforms; i++) {
		xform = genome->xform+i;
		xFormEntity = General/[NSEntityDescription insertNewObjectForEntityForName:@"General/XForm" inManagedObjectContext:moc];
		[xFormEntity setValue:General/[NSNumber numberWithInt:i] forKey:@"order"];
		[xFormEntity setValue:[Genome createVariationsEntitySetFromCXForm:xform inContext:moc] forKey:@"variations"];
		[xforms addObject:xFormEntity];	
		[xFormEntity release];
	}
	return xforms;
}

+ (General/NSMutableSet *)createVariationsEntitySetFromCXForm:(flam3_xform *)xform inContext:(General/NSManagedObjectContext *)moc {

	General/NSMutableSet *variations;
	General/NSManagedObject *variation;
	int j;
	
	/* create a variation entity */
	variations = General/[[NSMutableSet alloc] initWithCapacity:flam3_nvariations];

	for(j=0; j<flam3_nvariations; j++) {
		variation = General/[NSEntityDescription insertNewObjectForEntityForName:@"Variations" inManagedObjectContext:moc];
		
		[variation setValue:General/[NSString stringWithCString:flam3_variation_names[j] encoding:NSUTF8StringEncoding] forKey:@"variation_name"]; 
				
		[variations addObject:variation];
		[variation release];

	}
	return variations;	
}
@end


The only custom managed object is for the Genome Entity, it holds an General/NSColor, General/NSImage and General/NSAttributedString hence
is has the standard  code to store transients in binary persistant attributes.

    
@implementation General/GenomeManagedObject

- (void)willSave {

    General/NSImage *tmpImage = [self primitiveValueForKey:@"image"];
    if (tmpImage != nil) {
        [self setPrimitiveValue:General/[NSArchiver archivedDataWithRootObject:tmpImage] forKey:@"imageData"];
    } else {
        [self setPrimitiveValue:nil forKey:@"imageData"];
	}

    General/NSColor *tmpColour = [self primitiveValueForKey:@"background"];
    if (tmpColour != nil) {
        [self setPrimitiveValue:General/[NSArchiver archivedDataWithRootObject:tmpImage] forKey:@"backgroundData"];
    } else {
        [self setPrimitiveValue:nil forKey:@"backgroundData"];
	}

    General/NSAttributedString *tmpEdits = [self primitiveValueForKey:@"edits"];
    if (tmpEdits != nil) {
        [self setPrimitiveValue:General/[NSArchiver archivedDataWithRootObject:tmpEdits] forKey:@"editsData"];
    } else {
        [self setPrimitiveValue:nil forKey:@"editsData"];
	}
    [super willSave];
}

- (void)awakeFromFetch {

    General/NSData *imageData = [self valueForKey:@"imageData"];
    if (imageData != nil) {
        General/NSImage *image = General/[NSUnarchiver unarchiveObjectWithData:imageData];
        [self setPrimitiveValue:image forKey:@"image"];
    }

    General/NSData *colourData = [self valueForKey:@"backgroundData"];
    if (colourData != nil) {
        General/NSColor *colour = General/[NSUnarchiver unarchiveObjectWithData:colourData];
        [self setPrimitiveValue:colour forKey:@"background"];
    }

    General/NSData *editsData = [self valueForKey:@"editsData"];
    if (editsData != nil) {
        General/NSAttributedString *edits = General/[NSUnarchiver unarchiveObjectWithData:colourData];
        [self setPrimitiveValue:edits forKey:@"edits"];
    }
		
}

- (void)setImage:(General/NSImage *)newImage {

    [self willChangeValueForKey: @"image"];
    [self setPrimitiveValue:[newImage copy] forKey: @"image"];
    [self didChangeValueForKey: @"image"];
	
}

- (General/NSImage *)image {

    General/NSImage *image;

    [self willAccessValueForKey: @"image"];
    image = [self primitiveValueForKey: @"image"];
    [self didAccessValueForKey: @"image"];

    return image;
}

- (void)setBackground:(General/NSColor *)newColour {

    [self willChangeValueForKey: @"background"];
    [self setPrimitiveValue:[newColour copy] forKey: @"background"];
    [self didChangeValueForKey: @"background"];
	
}

- (General/NSImage *)background {

    General/NSImage *background;

    [self willAccessValueForKey: @"background"];
    background = [self primitiveValueForKey: @"background"];
    [self didAccessValueForKey: @"background"];

    return background;
}

- (void)setEdits:(General/NSAttributedString *)newEdits {

    [self willChangeValueForKey: @"edits"];
    [self setPrimitiveValue:[newEdits copy] forKey: @"edits"];
    [self didChangeValueForKey: @"edits"];
	
}

- (General/NSAttributedString *)edits {

    General/NSAttributedString *edits;

    [self willAccessValueForKey: @"edits"];
    edits = [self primitiveValueForKey: @"edits"];
    [self didAccessValueForKey: @"edits"];

    return edits;
}


@end



If I run the following code I get TEN entities, which matched the number of rows, returned, 
The extra enties are General/XForms, I'm beginning to think that insertNewObjectForEntityForName:inManagedObjectContext
is the root of my problem.

    

	General/NSArray *genomes;

	General/NSPredicate * predicate;                        
	General/NSFetchRequest *fetch = General/[[NSFetchRequest alloc] init];
	[fetch setEntity:General/[NSEntityDescription entityForName:@"Genome" inManagedObjectContext:moc]];
	genomes = [moc executeFetchRequest:fetch error:nil];
	[fetch release];



The code is in an action tied to the Render button in the cut down app, you'll need to run in Debug as there's no print code.

----

Replying to myself here... I brought the above code up on the cocoa-dev list and appearently
this is the expected behaviour for 'sub-entites'.  Since I had no idea what a 'sub-entity' was with
regards to core data or how I even made one I had to google around. Reading between the lines it
appears to have something to do setting the parent entity setting. I've have to look into this to comfirm 
it has I'm away from my Mac and have no recollection of using the Parent: drop down.
