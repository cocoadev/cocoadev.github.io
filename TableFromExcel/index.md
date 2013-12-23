---
layout: page
title: TableFromExcel
---

I'd like to load some data from a Mac Excel file. I was planning to load it into General/NSArray or General/KTMatrix.  However, I would like to load each cell by rows  into an array or Matrix.  So there should be a way to  represent the "end of  a row (of the table)" ?   Also, if any of the cell in Excel file is null, could I still store it into the array, or Matrix?

thank you very much for help,


Redmac

----

If each row is a fixed width, General/KTMatrix will do you fine, but I assume from the question they are variable. However, you *could* just take the largest width and top all the other rows up with nulls.

As for storing a null, that is what General/NSNull is there for. Hope that's been of some use.

-- General/KritTer


Hi General/KritTer, I appreciate you help. Number of colums of the table is fixed, but not for the row#. I could only find - readDataToEndOfFile - readDataOfLength
but non of them works for me at this point. 

How could I know the "width" of a row?  How I suppose to load datas in to a General/KTMatrix? Would you please give me a detail guide?  

I should metion that the data table I'm using contains null cells too. It might appears at the beginning, middle or end of any row/column (Thus I'm not able to get "width of the row"?). And I need to record them in order to do some analysis later on.

Thank you in advance,

Redmac

Sorry, by "width of row" I mean the number of cells in the row, including cells with null contents. This is bounded above by 65536 in Excel, IIRC, but you might be able to get a nicer bound for a given file.

To create a General/KTMatrix, call     
General/KTMutableMatrix *myMatrix
    = General/[KTMutableMatrix matrixWithCuboidBounds:colCount,
                                              rowCount,
                                              0];

where colCount could just be 65536, and rowCount the number of rows in the file, or 65536, whichever is easiest. To add an element, use     
[myMatrix setObject:anObject
      atCoordinates:column, row];

To have a null cell, either insert General/NSNull or, better, don't put any object there at all (this will save memory).

If you wish faster access to cells than O(log n) and more efficient memory usage, and you suspect that more than a third of your cells will be full, then get the colCount and rowCount bounds as low as possible and use the alternative constructor     
General/KTMutableMatrix *myMatrix
    = General/[KTMutableMatrix matrixWithCapacity:cellCount
                             cuboidBounds:colCount,
                                          rowCount,
                                          0];

where cellCount is the number of non-null cells in the table. Now the most efficient implementation will be used automatically. Of course, you will need to pre-parse your Excel file to determine these numbers.

If you wanted to use nested General/NSArrays instead of a single General/KTMatrix, you would need to use General/NSNull to mark null cells, but General/KTMatrix allows empty cells.

Finally, the version of General/KTMatrix accessible via http://www.geocities.com/kritter_cocoadev/General/KTMatrix/ is **not** the latest or most efficient; General/RobRix is currently in possession of that, and may be able to send it to you via email sometime.

If any of this was unclear, I am very happy to try explaining it better. Good luck with your coding!

-- General/KritTer

Thank you for the help. I'd appreciate if you or General/RobRix could send me a copy of  General/KTMatrix code to my email address: phyllis_feng@yahoo.com .

I am still confusing about how to read in the data from a formated data file(each of them are separated by tab space)? How could I iterate the raw data before I input them into any datastructure(like array or matrix)?

My data file will look like this:(there's no "----" in between, I use it to represent each colum space)

	
--------------C1	-------C2	----- C3	----- V(V)	----- C1	----- C2-----  C1----- C2	----- V(V)

angels	----?----------a~--------n---------ts--------@

apples----------------�----------{---------b	---------e

egg--------------------?----------eI					

elephant	-------------?----------E	-------------------?--------------------E

 
. . .

-- Redmac

Are you asking how to read in text files in Cocoa? -- General/KritTer
�

No,  because my source files contain empty strings (tabs) and they are important. I have to record them and their location.  When I read in the file, I do not know how to save those Nil object and their position correctly in the array or matrix.

Following is the code I've done:
    
#import <Foundation/Foundation.h>


int main (int argc, const char * argv[]) {
    
    General/NSAutoreleasePool * pool = General/[[NSAutoreleasePool alloc] init];
   
    
    General/NSString *filename;
    General/NSString *source;
    General/NSArray * rowarray;
    General/NSArray * colarray;
    int count ;
    int i;
    General/NSRange *range;
    int colnum; //colum number
    
    filename = @"~/01PhyllisF/load/wzdata.txt";
    filename=[filename stringByStandardizingPath];
    printf("%s\n", [filename UTF8String]);
    
    source=General/[NSString stringWithContentsOfFile:filename];
    rowarray =[source componentsSeparatedByString:@"\r"];
    count =[rowarray count];
   
     for (i=0; i< count; i++)
    {
      printf("%s\n", General/rowarray objectAtIndex:i] UTF8String]);    
     }
     
     colarray = [[rowarray objectAtIndex:1] componentsSeparatedByString:@" "];
     colnum =[colarray count];
     
    printf("%s\n", [[colarray objectAtIndex:0] UTF8String]);  
    printf("row: %d\n column: %d\n", count, colnum);
    
    [pool release];
    return 0;
}


I could end up only have the rowarray works. The colarray only has one components. Seems like components is not separated successfully.

result:
    
A-FEMALE: JAN/00				MEDIAL-1					MEDIAL-2							
	INITIAL				SFWW		SIWW			SF	SIWW				FINAL		
	C1	C2	C3	V(V)	C1	C2	C1	C2	V(V)	C1	C1	C2	V(V)	C1	C2	C3	
angels		?		a~	n		ts		@					?<pi>	?<pi>		
apples		?<pi>		{			b		e					?<pi>	?<pi>		
egg		?		eI										k=			
elephant		?		E			?		E		?		E	?<pi>	?<pi>		I
elephant		?		E			l		E		j		a	p=	?<pi>		C
orange		?		o~n			?		I					z			
matches		m		{			tS		?<pi>					?<pi>			
monkey		m		{~	?<pi>		k		i								
knife		l		E										p			
knife		?		E										f			
knife		?		E										f:			
nails		n		eI										?<pi>	?<pi> (creaky)		
pencil		p=		E~	(n)		s8		@					?<pi>			
pipe		p		V										p			
potato		p		U:			?<pi>		?<pi>		?<pi>		?<pi>	(creaky)			
baby		p		i			p		I								
balloons		b		?<pi>			?<pi>		u~					n	z		
bananas		?		@			?		{		?		@				
bathtub		b		{	Tt		d		a:					?<pi>			
bed		b		E										d(devoiced)			
bell		b		E										?<pi>			
bird		b		O@										?			
book		b		U										k			
brush		b	?<pi>	V										tS			
boy		b		O										?			
table		t		eI			?<pi>		@ (creaky)								
teeth		t=		@I										?<pi>			
toothbrush		t		u:	?<pi>		?<pi>	?<pi>	?<pi>					?<pi>			
train		t	?<pi>	e										?<pi>			
dog		d		a										k			
cake		k=		E										k=			
can		k=		{~										n			
car		k=j		{:										?<pi>			
carrots		k=		E@			?<pi>		?<pi>					?<pi>	?<pi>		
cat		kX		{										t=			
comb		?		EU~										m			
crackers		k=	?<pi>	{			?		@					s			
crayon		k=	?<pi>	E			?<pi>		E	(2 syl)				?<pi>			
cup		g		{										p			
keys		h		eI													
keys		k=:		i													
gun		k		@			n		@								
feathers		f		E			v/D		@					?<pi>			
fish		f		I										S			
flag		f	?<pi>	aI										?<pi>			
flowers		f	?<pi>	aU			v		@					?<pi>			
fork		f		@										?<pi>	k=		
vacuum		v		{			k	?<pi>	@					?<pi>			
thumb		Tl		E~		(lateralized th)								?<pi>			
that		d8		{										t			
saw		s		a:										k=			
scissors		t=		@			?<pi>		@					?<pi>			
skates	?<pi>	k=		E										t	s:		
spoon	?<pi>	p=:		U:													
stars	s	t8		A:										?<pi>	?<pi>		
station	?<pi>	t		eI			?<pi>		eI: (creaky voice)								
stove	?<pi>	t		@			v		E@								
swing	s...	k		@:										?			
zipper		?		I			?<pi>		@								
shoe		t=s		U:													
chair		tS		eI:~										?<pi>			
jars		s		a:													
radio		?<pi>		E@			?<pi>		?<pi>		?		@				
ladder		l		{			d		@								
lamp		j		{~										m	p		
blocks		b	?<pi>	V										k			
clock		k	?<pi>	V										?			
wagon		?		@			g		@					?<pi>			
whistle		h		I			ts		I:					?<pi>			
witch		?		I										tS			
hammer		h		{			m		E@								
hanger		?		@:	?<pi>				E								
hat		h		{										t=			
house		?		V										ts			
yes		n		@										ks			

	INITIAL				SFWW		SIWW			SF	SIWW				FINAL		 //(this is the colarray[0], and the only component in colarray.)
 
 row: 79
 column: 1



many thanks, --- Redmac

I solve the problem. Save the excel file of myfile.csv format. 
use 
componentsSeparatedByString:@","];

instead using "space " as the separated string.
Then contents of every single cell will be nicely parsed.

Thanks
-- Redmac

Good, glad you have it sorted :)  I've orphaned this page now. If you feel your code/ideas may benefit others, would you mind refactoring them into a page somewhere, Redmac? -- [[KritTer

_____________________________________________________________________________________________

Actually this does not solve the problem if any of the cells contains a comma.  The csv file will have those commas contained in quotes, or double quotes, but using componentsSeparatedByString:@","] will still yield too many columns for a row where any column in that row originally contained a comma.

So has anyone written a csv parsing routine?  hmm, I do not know how to refactor this back into the wiki, so I think no one will see this 

----

General/ReadWriteCSVAndTSV

----
I'm interested in parsing excel files directly, so the user doesn't have to export first to .csv format.  Is anyone in possession of knowledge which might help me on this quest?  I plead with you to share it with me...

----
The .xls format is available along with the other Office file formats from Microsoft, here:

http://www.microsoft.com/interop/docs/General/OfficeBinaryFormats.mspx

However: abandon hope, ye who enter. The formats are crufty, bloated, complicated, and simply horrible in every way.
