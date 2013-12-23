---
layout: page
title: VersioningUsingCoder
---

This is pulled from an e-mail on the gnustep-dev mailing list:

http://mail.gnu.org/pipermail/gnustep-dev/2001-October/000401.html

The way to do versioning of objects encoded using General/NSCoder is to set the current version of the class in the initialize method. Then when the class is encoded it will be written out with that version number. When you decode the class, you can check for old version numbers.

For example:

    
static const int	currentVersion = xxx;		// Current version

+ (void) initialize
{
   if (self == General/[MyClass class])
     {
       [self setVersion: currentVersion];
     }
}

- (id) initWithCoder: (General/NSCoder*)aCoder
{
   int	version = [aCoder versionForClassName:@"General/ClassName"];

   self = [super initWithCoder:aCoder];
   if (self) {

      if (version < currentVersion)
        {
           ....
	   	//	Here you would set default values for the new instanceVariables 
	   	//	(ie the new ones in this version of the class - that wouldn't exist in a previous version of the class.)
   
		//	You probably would want to have several of these if statements nested, checking for each version change 
		//	and setting only the new values for that change - then you can have graceful versioning for any 
		//	outdated encoded instance of your class. 
        }
   }

   return self;
}


Typically, this sort of thing should be done to handle changes in 
archiving formats between and within releases of the libraries.

When we make a release of the base library, we update the system 
version ... and this makes it possible for us to write much more efficient code: **[Author's note: I don't understand the code below...Anyone care to fill in details or explanation?]**

    

- (id) initWithCoder: (General/NSCoder*)aCoder
{
   int	version = [aCoder versionForClassName: @"General/MyClass"]; 

   self = [super initWithCoder:coder];

   if (self) {
      if (systemVersion <= lastVersionWithFormatChanges)
        {
          version = [aCoder versionForClassName: General/NSStringFromClass([self 
class])];
        }
      else
        {
          version = currentVersion;
        }

     if (version < currentVersion)
        {
           ....
        }
   }

   return self;
}



----

Yeah -- I don't get it either. The "systemVersion" message returns the version of the system used to make the archive. By system I mean Operating System, like General/NextStep, General/OpenStep, Rhapsody, General/MacOSX, etc. Seems to me that information is only relevant to test for the existence of the data required by "versionForClassName:". And since that's always available for non-keyed archives on Mac OS X, why bother?

Of course, they could be using their own General/NSCoder subclasses and can redefine systemVersion to mean whatever they want. In which case we won't understand your example until they include the General/NSCoder subclasses. I think that might be the case from some of the other context in the message 

Finally, it's worth mentioning that the built-in versioning scheme ("versionForClassName:"  etc.) only seems to work with non-keyed archives. When using keyed archives you'll need to supply your own versioning mechanism.

Oh -- also, General/PierreYves writes:

    
- (id) initWithCoder: (General/NSCoder*)aCoder
{
   // ** the following line is wrong **
   // int	version = [aCoder versionForClassName: General/NSStringFromClass([self class])];
   // ** we should rather write the following **
   int	version = [aCoder versionForClassName: @"General/MyClass"]; 
   // ** otherwise, there would be a lot of problem when encoding 
   //  subclasses -- General/PierreYves**


I agree, and have cleaned up the original post so it reads correctly.

Also, the original code doesn't account for superclasses. As in any "init" routine, you must call "super" to set the value of "self". Again, I have adjusted the original post so it reads correctly.

-- General/MikeTrent
