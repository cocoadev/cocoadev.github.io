---
layout: page
title: TimingWithDeferredLogging
---

Here are some simple Macros that allow blocks of code to be timed, then logged in bulk later on with the duration and assigned name of each step.
This results in simpler code, cleaner logging output, and removes the delay from the timing that General/NSLog or printf can sometimes cause.
An example is shown below.

The base set of macros uses a default variable in the current scope to store timings (on the stack).
The custom set allows a variable name to be specified; these have the suffix '_VAR'.

Timing variables are declared with a constant capacity, though they can be reused with a new name by using the BEGIN macro again.

-- General/QwertyDenzel

----
**Header**
    
/* Imports:
 *  General/CoreFoundation
 */
#import <General/CoreFoundation/General/CoreFoundation.h>


// With custom variable.
#define LOGTIME_DECLARE_VAR(varname, count) struct {General/CFAbsoluteTime t[count+1]; size_t c; char *names[count+1];} varname
#define LOGTIME_BEGIN_VAR(varname, name) varname.t[0] = General/CFAbsoluteTimeGetCurrent(), varname.names[0] = name, varname.c = 1
#define LOGTIME_NEXT_VAR(varname, name) varname.t[varname.c] = General/CFAbsoluteTimeGetCurrent(), varname.names[varname.c] = name, varname.c++;
#define LOGTIME_PRINT_VAR(varname) do {printf("### %s ###\n", varname.names[0]);\
size_t i;\
for (i = 1; i < varname.c; i++) {printf("\t%s %f\n", varname.names[i], varname.t[i] - varname.t[i-1]);}} while (0)

// Using shared variable name.
#define LOGTIME_DECLARE(count) LOGTIME_DECLARE_VAR(_logtime, count)
#define LOGTIME_BEGIN(name) LOGTIME_BEGIN_VAR(_logtime, name)
#define LOGTIME_NEXT(name) LOGTIME_NEXT_VAR(_logtime, name)
#define LOGTIME_PRINT() LOGTIME_PRINT_VAR(_logtime)


----
**Example**

Here's a dummy example outlining its key usage. Hopefully your code will look a bit prettier than this. :)

    
- (void)performOperations
{
	LOGTIME_DECLARE(2);
	
	// Begin data operations:
	LOGTIME_BEGIN("Data operations");
	
	/*** Reading ***/
	LOGTIME_DECLARE_VAR(readTimings, 3);
	LOGTIME_BEGIN_VAR(readTimings, "Reading");
	
	General/NSData *data = [self readData];
	LOGTIME_NEXT_VAR(readTimings, "Read data");
	
	[self createObjectsFromData:data];
	LOGTIME_NEXT_VAR(readTimings, "Create objects");
	
	[self sortObjects];
	LOGTIME_NEXT_VAR(readTimings, "Sort objects");
	
	// Finish all reading operations.
	LOGTIME_NEXT("Reading");
	
	/*** Writing ***/
	LOGTIME_DECLARE_VAR(writeTimings, 3);
	LOGTIME_BEGIN_VAR(writeTimings, "Writing");
	
	[self removeNonEssentialInformation];
	LOGTIME_NEXT_VAR(writeTimings, "Reducing");
	
	data = [self createCompressedData];
	LOGTIME_NEXT_VAR(writeTimings, "Create data");
	
	[self writeData:data];
	LOGTIME_NEXT_VAR(writeTimings, "Write data");
	
	// Finish all writing operations.
	LOGTIME_NEXT("Writing");
	
	/*** Print timings ***/
	// Total:
	LOGTIME_PRINT();
	// Reading:
	LOGTIME_PRINT_VAR(readTimings);
	// Writing:
	LOGTIME_PRINT_VAR(writeTimings);
}

/*
Results:
### Data operations ###
	Reading: 1.050202
	Writing: 1.550205
### Reading ###
	Read data: 0.600073
	Create objects: 0.300039
	Sort objects: 0.150085
### Writing ###
	Reducing: 0.450063
	Create data: 0.250055
	Write data: 0.850084
*/


