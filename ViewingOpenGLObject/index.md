---
layout: page
title: ViewingOpenGLObject
---

I read this article about General/GlobalVariablesInCocoa. I try to make a global variable to share the General/NSMutableArray in the new nib i created at runtime. I create 3 classes to achieve this:

    
// globalValues.h

#import <Cocoa/Cocoa.h>

extern General/NSMutableArray *vertexOri, *vertexTemp;

@interface globalValues : General/NSObject {}
@end
// end of globalValues.h

// globalValues.m
#import "globalValues.h"

General/NSMutableArray *vertexOri;
General/NSMutableArray *vertexTemp;

@implementation globalValues
@end
// end of globalValues.m


i use this globalValues class in two different class 

    
// General/QuiXplorer.h
#import <Cocoa/Cocoa.h>
#import "vertex.h"
#import "glViewerController.h"

@interface General/QuiXplorerController : General/NSObject
{
	General/IBOutlet General/NSPanel *mainMenu;
	General/IBOutlet General/NSButton *loadButton;
	General/IBOutlet General/NSButton *saveButton;
	General/IBOutlet General/NSButton *closeButton;
	
	glViewerController *doc;
}

- (General/IBAction)General/LoadFile:(id)sender;
@end
// end of General/QuiXplorer.h

// General/QuiXplorer.m
#import "General/QuiXplorerController.h"
#import "globalValues.h"

@implementation General/QuiXplorerController
- (General/IBAction)General/LoadFile:(id)sender
{
	General/NSLog(@"Load File");
	
	General/NSString *objFile;
	if (objFile = General/[NSString stringWithContentsOfFile:[self getUserFileName:0]])
	{
		// Setting the buttons
		[loadButton setEnabled:NO];
		[saveButton setEnabled:YES];
		[closeButton setEnabled:YES];
		
		// Extracting vertex from file;
		General/NSScanner *scanner;
		General/NSCharacterSet *newLineCharacterSet = General/[NSCharacterSet characterSetWithCharactersInString:@"\r\n"];
		
		float x,y,z;
		int jmhTitik = 0;
		int jmhVn = 0;
		int jmhF = 0;
		vertex *ttk = General/vertex alloc] init];
		vertexOri = [[[[NSMutableArray alloc] init];		

		scanner = General/[NSScanner scannerWithString:objFile];	
		while ([scanner isAtEnd] == NO)
		{
			// Scanning Vektor
			if ([scanner scanString:@"v " intoString:nil])
			{
				[scanner scanFloat:&x];
				[scanner scanFloat:&y];
				[scanner scanFloat:&z];
				
				[ttk setVertexX:x setVertexY:y setVertexZ:z];

				[vertexOri addObject:ttk];
				jmhTitik++;
			} 
			// Scanning vektor normal
			else if ([scanner scanString:@"vn " intoString:nil])
			{
				[scanner scanFloat:&x];
				[scanner scanFloat:&y];
				[scanner scanFloat:&z];
				jmhVn++;
			}
			// Scanning f
			else if ([scanner scanString:@"f " intoString:nil])
			{
				[scanner scanInt:nil];
				[scanner scanInt:nil];
				[scanner scanInt:nil];
				jmhF++;
			} 
			// Scanning for newline
			else
			{
				[scanner scanUpToCharactersFromSet:newLineCharacterSet intoString:nil];
			}
		}
					
		General/NSLog(@"ttk terakhir : \n x=%f \n y=%f \n z=%f",General/vertexOri objectAtIndex:jmhTitik-1] getX],[[vertexOri objectAtIndex:jmhTitik-1] getY],[[vertexOri objectAtIndex:jmhTitik-1] getZ]);
		
		// releasing variables from memory
		[ttk dealloc];		

		[[[NSBundle loadNibNamed:@"glViewer" owner:self];
	}
}
@end
// end of General/QuiXplorer.m


and the last class is where i call the new nib
    
// glViewerController.h
#import <Cocoa/Cocoa.h>
#import "glViewerView.h"
#import "vertex.h"

@interface glViewerController : General/NSResponder
{
	General/IBOutlet id glWindow;
	glViewerView *glView;
}

- (void)awakeFromNib;
// end of glViewerController.h

// glViewerController.m
- (void)awakeFromNib
{
	[ General/NSApp setDelegate:self ];   // We want delegate notifications
	[ glWindow makeFirstResponder:self ];
	glView = [ [ glViewerView alloc ] init];
	if ( glView != nil )
	{
		[ glWindow setContentView:glView ];
		[ glWindow makeKeyAndOrderFront:self ];
		
		vertex *titik = General/vertex alloc] init];

		int jmhTtk = [vertexOri count];
		[[NSLog(@"jmh titik glviewer controller = %d",jmhTtk);
		int ul = 0;

		for (ul = 0 ; ul < jmhTtk ; ul++)
		{
			titik = [vertexOri objectAtIndex:1];
			General/NSLog(@"x = %f , y = %f , z = %f",[titik getX],[titik getY],[titik getZ]);
		}
	}
	else
	{
		General/NSLog(@"Failed to create Window");
	}
}
// end of glViewerController.m


then when i run the code and do the action here is what i get in the General/NSLog :

    
2005-11-14 18:06:58.089 3D General/QuiXplorer[3527] jmh titik glviewer controller = 10
2005-11-14 18:06:58.108 3D General/QuiXplorer[3527] *** -General/[NSCheapMutableString getX]: selector not recognized [self = 0x3dbe60]
2005-11-14 18:06:58.124 3D General/QuiXplorer[3527] *** -General/[NSCheapMutableString getX]: selector not recognized [self = 0x3dbe60]


where do i do wrong? thanks - General/JuliusJuarsa

----

The error message looks pretty self-explanatory: you're calling getX on a string. Not sure why.

You should NOT allocate only one vertex and repeatedly call     -setVertexX:setVertexY:setVertexZ: on it. Try using:

    
// Scanning Vektor
if ([scanner scanString:@"v " intoString:nil])
{
   vertex *ttk = General/vertex alloc] init];

   [scanner scanFloat:&x];
   [scanner scanFloat:&y];
   [scanner scanFloat:&z];
   [ttk setVertexX:x setVertexY:y setVertexZ:z];

   [vertexOri addObject:ttk];
   [ttk release];
   jmhTitik++;
} 


You probably want to capitalize the class     vertex, too: use     Vertex to be clearer. Ditto for     globalValues.

I also recommend you move     vertexOri *out* of your global variables. Make it a class member of [[QuiXplorerController. Don't use globals unless you're sure.

The problem is the line     [ttk dealloc]. Go read General/MemoryManagement. Don't ever call     dealloc directly: use     release. -- General/KritTer

One more thing, style-wise. Instead of providing separate     -init and     -setVertexX:setVertexY:setVertexZ: methods - the former of which can do nothing sensible - try providing one combined method,     -initWithVertexX:setVertexY:setVertexZ:. If you want a mutable vertex class,     General/MutableVertex, you can then make it a General/SubClass of     Vertex. -- General/KritTer

----

The reason i use global variable on General/VertexOri is that i want to use the General/NSMutableArray value on 2 different class. is there any other way of doing this than using the extern keyword??
to General/KritTer : i replace the     [ttk dealloc] with     [ttk release] like your suggestion and it's working. but my question is what is the different? i am confused with the memory management stuff ^^

in cocoa, can i do 2 different initialization but with different parameter?? 
ex :     -initWithVertexX:setVertexY:setVertexZ:
          -initWithVertexX:getVertexY:getVertexZ:

correct me if i'm wrong. so in General/NSMutableArray, if I addObject: in the array (example Vertex) at index 1, when i retreive the object at index 1 i would get the same Vertex? - General/JuliusJuarsa

----
The syntax is slightly off; it would be     -initWithVertexX:vertexY:vertexZ:. Then you have accessors     -vertexX,     -vertexY,     -vertexZ, and     -getVertexX:vertexY:vertexZ:. The mutators would be     -setVertexX:,     -setVertexY:,     -setVertexZ:, and     -setVertexX:vertexY:vertexZ:. This is pretty much the standard format for a multiple-variable access: individual accessors and mutators, with a single init method. Also, you should only have one verb per method.     initWith... means you are using the nouns following "with" to initialize the object.     set... is similar.     get... means you are returning by passed-in references (pointers). No verb means you are returning by value. For more information, check out the great naming stuff on http://www.cocoadevcentral.com/articles/000082.php --General/JediKnil

----
Thanks, Jedi, that was a copy-n-paste error on my part
----

If you want to access the array of vectors from another object, you need to find a way to give that object access to the General/QuiXplorerController. If you're setting this up from General/InterfaceBuilder, you can add an outlet to the second class and link it to the controller. Alternatively, if the second object's method is called from the controller, the controller can pass the array of vectors as a parameter.

Retain/release are balanced pairs. When you pass an object to an array, the array retains it. When you remove an object from an array (or the array is deallocated after its final release), it releases it. When you create the object, you are also implicitly retaining it once, so when you've added it to the array, you should call     release on it if you no longer need to access it. When the object has been released as many times as it was retained, it will     dealloc itself, because (unless you've coded things incorrectly) it knows nobody wants it anymore.

Arrays store the object passed to them, **not copies**. If you call     dealloc directly, you bypass this General/MemoryManagement system, and delete the object held in the array. When you access the array again later, the memory the object used to take up will quite likely have been reused. In your code, it had been taken up by a string, hence the strange error. As you said, when you call     addObject: then later retrieve the object at that location, you get **the same Vertex** that you put in. -- General/KritTer
