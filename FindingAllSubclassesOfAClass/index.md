---
layout: page
title: FindingAllSubclassesOfAClass
---



What I would really like to be able to do is find each of the subclasses (not instances) of a particular class so that I can call something like the following. http://goo.gl/OeSCu

    
+ (void)registerAllTools
{
    NSEnumerator *objectEnumerator = self subclasses] objectEnumerator];
    id object;

    while( object = [objectEnumerator nextObject] )
    {
        [object registerTool];
    }
}


I would like to implement a method that is implemented by the class, but is executed only by that class's subclasses. This would save me a lot of repetitous code in many places.

Thanks for any suggestions
- [[EliotSimcoe

----

NSObject+SubclassEnumeration.h

    
#import <Foundation/Foundation.h>

@interface NSObject (SubclassEnumeration)
+ (NSEnumerator*)subclasses;
+ (NSEnumerator*)directSubclasses;
@end


NSObject+SubclassEnumeration.m

    
#import "NSObject+SubclassEnumeration.h"
#import <objc/objc-runtime.h>

@implementation NSObject (SubclassEnumeration)

+ (NSEnumerator*)subclasses
{
    NSMutableArray    *tempArray;
    NSArray           *resultArray;
    Class             *classes;
    struct objc_class *superClass;
    Class             *current;
    int                count, newCount, index;

    tempArray   = [[NSMutableArray allocWithZone:nil] initWithCapacity:12];
    resultArray = nil;

    if (tempArray)
    {
        classes = NULL;
        count   = objc_getClassList(NULL, 0);
        if (count)
        {
            classes = malloc(sizeof(Class) * count);
            if (classes)
            {
                newCount = objc_getClassList(classes, count);
                while (count < newCount)
                {
                    count = newCount;
                    free(classes);
                    classes = malloc(sizeof(Class) * count);
                    if (classes)
                        newCount = objc_getClassList(classes, count);
                }
                count = newCount;
            }
        }
        
        if (classes)
        {
            const Class thisClass = self;
            current = classes;
            
            for (index = 0; index < count; ++index)
            {
                superClass = (*current)->super_class;
                if (superClass)
                {
                    do
                    {
                        if (superClass == thisClass)
                        {
                            [tempArray addObject:*current];
                            break;
                        }
                        superClass = superClass->super_class;
                    } while (superClass);
                }
                
                ++current;
            }
            
            free(classes);
        }

        resultArray = [NSArray arrayWithArray:tempArray];
        [tempArray release];
    }

    return (resultArray) ? [resultArray objectEnumerator] : nil;
}

+ (NSEnumerator*)directSubclasses
{
    NSMutableArray *tempArray;
    NSArray        *resultArray;
    Class          *classes;
    Class          *current;
    int             count, newCount, index;

    tempArray   = [[NSMutableArray allocWithZone:nil] initWithCapacity:12];
    resultArray = nil;

    if (tempArray)
    {
        classes = NULL;
        count   = objc_getClassList(NULL, 0);
        if (count)
        {
            classes = malloc(sizeof(Class) * count);
            if (classes)
            {
                newCount = objc_getClassList(classes, count);
                while (count < newCount)
                {
                    count = newCount;
                    free(classes);
                    classes = malloc(sizeof(Class) * count);
                    if (classes)
                        newCount = objc_getClassList(classes, count);
                }
                count = newCount;
            }
        }
        
        if (classes)
        {
            const Class thisClass = self;
            current = classes;
            
            for (index = 0; index < count; ++index)
            {
                if ((*current)->super_class == thisClass)
                    [tempArray addObject:*current];
                ++current;
            }
            
            free(classes);
        }

        resultArray = [NSArray arrayWithArray:tempArray];
        [tempArray release];
    }

    return (resultArray) ? [resultArray objectEnumerator] : nil;
}

@end


--NeilVanNote

----

Wow... I was expecting some pointers, but this is incredible! Should this be moved to the sample code section? Thank you so much, to whomever posted this :-)
Just one question. Is there a reason you initialized the mutable array with a capacity of 12? Why not just use its -init method? --EliotSimcoe

----

I picked 12 out of thin air to give the NSMutableArray a running start. -init would suffice.

--NeilVanNote

----

I added one last method which I have found to be very useful. I don't think I'm leaking any memory, but I wouldn't mind a second check if anyone is willing. Any optimizations to the algorithm are also more than welcome. It is kind of simplistic as it stands. This method finds all classes at the "end" of the class tree.

    
+ (NSArray *)terminalSubclasses
{
    NSMutableArray *subclasses = [[NSMutableArray allocWithZone:nil] init];
    
    if( subclasses )
    {
        int listCount = objc_getClassList( NULL, 0 );
        Class *classes = NULL;
        int classCount = 0;
        
        if( listCount )
        {
            if( classes = malloc( sizeof( Class ) * listCount ) )
            {
                listCount = objc_getClassList( classes, listCount );
                
                while( classCount < listCount )
                {
                    // don't leak memory
                    free( classes );
                    
                    classes = malloc( sizeof( Class ) * listCount );
                    classCount = listCount;
                    
                    if( classes ) { listCount = objc_getClassList( classes, classCount ); }
                }
                
                // make sure that we have exactly as
                // many classes as we think we do
                classCount = listCount;
            }
        }
        
        // if classes are available
        if( classes )
        {
            const Class selfClass = self;
            struct objc_class *superclass;
            Class *allSubclasses = NULL;
            Class nextClass = NULL;
            Class *class = classes;
            Class *classPtr = NULL;
            BOOL test = NO;
            int count = 0;
            int i;
            
            // iterate through the classes we found, checking if
            // we are one of their superclasses
            for( i = 0 ; i < classCount ; i++ )
            {
                if( superclass = (*class)->super_class )
                {
                    do
                    {
                        if( superclass == selfClass )
                        {
                            int size = sizeof( Class ) * ++count;
                            Class *buffer = allSubclasses;
                            
                            allSubclasses = malloc( size );
                            if( allSubclasses )
                            {
                                if( buffer )
                                {
                                    memcpy( allSubclasses, buffer, malloc_size( buffer ) );
                                }
                            
                                // copy the next class
                                allSubclasses[count - 1] = (*class);
                            }
                            if( buffer ) { free( buffer ); }
                            
                            break;
                        }
                        
                        superclass = superclass->super_class;
                    }
                    while( superclass );
                }
                
                // increment the current class
                class++;
            }
            
            // now that we have all of the classes we want
            // to keep only those that are't a superclass
            // of any of the others... easier said than done
            //
            // ensure that class is not a superclass of any classPtr's
            //
            class = allSubclasses;
            for( i = 0 ; i < count ; i++ )
            {
                classPtr = allSubclasses;
                test = NO;
                int j;
                
                for( j = 0 ; j < count ; j++ )
                {
                    // get a new pointer to classPtr that will
                    // be used to iterate through its superclasses
                    nextClass = (*classPtr);
                    
                    // compare all of classPtr's superclasses to class
                    do
                    {
                        if( (nextClass = (nextClass->super_class)) == (*class) )
                        {
                            test = YES;
                            break;
                        }
                    }
                    while( nextClass != selfClass );

                    if( test ) { break; }
                    
                    classPtr++;
                }
                
                if( !test )
                {
                    [subclasses addObject:(*class)];
                }
                
                class++;
            }
            
            // release all of the classes that were found
            if( allSubclasses ) { free( allSubclasses ); }
            free( classes );
        }
    }
    
    return (NSArray *)[subclasses autorelease];
}



Thanks for any comments --EliotSimcoe

----

You should really be double checking the result of your allocations. They can fail, and they will surely fail as soon as your app is in the hands of a client/customer. ;-) Objective-C can to be pretty forgiving about calling methods on nil instances, but your code will surely fault as soon as you try to write to a memory location that isn't yours to write to.

I admit, my original methods did have a possible memory leak. In the event realloc fails, it doesn't free the pointer I gave it. I have since updated the code to use reallocf which should do the trick. I should really bite the bullet and replace the mess with a malloc/free dance since the loop doesn't need the copy semantics of realloc anyways.

--NeilVanNote

----

This is my last adjustment for this one. Changed the initial allocations to rid it of realloc/reallocf; and made a sanity assignment to the count just in case objc_getClassList decides to bring back a smaller number of classes than I was expecting on the retry. Should be fairly bullet-proof now.

Cheers,

--NeilVanNote

----

Is this really legal? From http://gcc.gnu.org/onlinedocs/gcc-3.0.4/gcc_7.html it says:

7.1.1 What you can and what you cannot do in +load

The +load implementation in the GNU runtime guarantees you the following things:

*...
*you can allocate and send messages to objects whose class is implemented in the same file;
*...


In particular, the following things, even if they can work in a particular case, are not guaranteed:

*...
*allocation of or sending messages to arbitrary objects;
*...


In the code above, messages are sent to arbitrary objects (the subclasses), unless they are declared in the same file.

--AllanOdgaard

----

Then it's not legal (my bad... I actually kind of thought that it might not be...) but I am still interested in this category that is forming. To remedy the illegality of the above code, I am going to place the above in a +register method that gets called by the controlling class. I made a couple of corrections to my code above, and I think it's a lot better now. I would however, appreciate it if anyone could point out any further flaws in it.  Thanks again --EliotSimcoe

----

Nope, technically it's not legal to do this from +load. During the initial development I recognized the problems executing the methods from +load. Since I had the Categories started, I submitted them as a partial solution. Also, considering the very dynamic nature of the Objective-C runtime, you can only treat the enumerations that these bring back as a snapshot of "what was" at any given point in time anyways.

It's not anything the category methods are doing that illegal, it's what what the caller does with the information if called from +load, afterwards... ;-)

----

I realize this. That's why I changed my calling method from +load to +registerAllTools (which is called from -applicationWillFinishLaunching).  Anyway, does anyone care to punch some holes in my implementation of +terminalSubclasses? I always like second, third and fourth opinions. :-D --EliotSimcoe

----

Hello. Is it just me, or is some of this code excessively verbose and unnecessary? For example, the first function will potentially call objc_getClassList more than once, as if the number of classes would ever change? Or am I just not seeing something here. Thanks for your code. - Sammi

*It might. ObjC supports dynamic loading of bundles, which can add classes to the runtime. This is actually a great feature, and supports the creation of plugins. For this example, say that you have a class, **Plug<nowiki/>In**, which represents a generic addition to your application (you would probably use a protocol for this, but that wouldn't fit this page). You then open a kind of plugin manager window, which uses     +subclasses to list all available plugins (again, probably not the best solution). Then you download this cool new plugin that allows you to...do something cool. So you load it into the application, and suddenly you have another subclass. Instead of having to restart the program, you can now use ObjC's dynamism to change the list of classes. Sooooo...the number of classes can change during an application's lifespan. --JediKnil (We now return to our regular, on-topic discussion that I so rudely interrupted)*

Look at the code though - it's calling it 3 times from the same method. There's probably a reason for that, but I don't know what it is.

----

It is nice to be able to use the run-time to find lots of information.  However, I can't help thinking that finding subclasses of a particular class is not a good way to find plug-ins.

Why not have the plug-in base class store a collection of plug-in classes and have each plug-in class register.

@implementation Plugin : NSObject

static NSMutableSet   *pluginClasses = nil;

+ registerPluginClass:(Class)aClass
{
  if(nil == pluginClasses)
  {
    pluginClasses = [[NSMutableSet alloc] init];
  }
  [pluginClasses addObject:aClass];
}

+ initialize
{
  [self registerPluginClass:self];
}

@end

Now all subclasses of Plugin will automatically register themselves with moma the first time the runtime sees the class.
Use pluginClasses in other methods to access the set of known plugin classes.

In addition to using a lot less code than searching the runtime data structures, this approach is less invasive, works with multiple runtimes, can be polymorphically customized in the future, does not have any explicit opportunities for seg-faults, and still does not require plugin writers to register explicitly...  It is automatic.

*True...it was a bad example. I know it's really not a good idea to implement a plugin architecture this way. However, I just wanted to give an example of how to use     +subclasses in a dynamic runtime, if not when. --JediKnil*

----

Here is a much better implementation of the above code:

    

+ (NSArray*) subclasses {
    NSMutableArray		*subclasses;
    struct objc_class	*superClass;
    Class				*classes = NULL;
    Class				*current;
	const Class			thisClass = self;
    int					count, index;

    subclasses = [[NSMutableArray alloc] initWithCapacity:12];

	count = objc_getClassList(NULL, 0);
		
	if (count) {
		classes = malloc(sizeof(Class) * count);
		NSAssert (classes != NULL, @"Memory Allocation Failed in [NSObject +subclasses].");

		(void) objc_getClassList(classes, count);
	}

	if (classes) {
		current = classes;

		for (index = 0; index < count; ++index, ++current) {
			superClass = *current;
			while (superClass = (superClass)->super_class)
				if (superClass == thisClass) {
					[subclasses addObject:*current];
					break;
				}
		}

		free(classes);
	}

    return subclasses;
}



The initial poster's code isn't too good, this version works from it and is probably a bit faster and more understandable. No offence to the initial poster. - Sammi

----

No offense taken. The code posted directly above is error-prone because the number of classes can change with each invocation of objc_getClassList. It may work today, it won't work for the user someday...

----
What do you anticipate will break? If classes are removed (which is currently not supported by the runtime) then objc_getClassList will simply return the remaining classes. If classes are added, then it will miss some, but if you have other threads running that are adding classes while you're doing this, then you *always* have the possibility of missing some new classes in this situation, no matter how your "get all classes" code is written.

----
Here follows a new version of the code posted above. It is even more simple and contains no references to deprecated code:

    

+(NSArray *)subclasses
{
	NSMutableArray *subClasses = [NSMutableArray array];
	Class				   *classes    = nil;
	int             count      = objc_getClassList(NULL, 0);
	
	if (count) {
		classes = malloc(sizeof(Class) * count);
		NSAssert (classes != NULL, @"Memory Allocation Failed in [Content +subclasses].");
		
		(void) objc_getClassList(classes, count);
	}
	
	if (classes) {
		for (int i=0; i<count; i++) {
			Class myClass    = classes[i];
			Class superClass = class_getSuperclass(myClass);
			
			if (superClass == self)
				[subClasses addObject:myClass];
		}
		
		free(classes);
	}
	
	return subClasses;
}

http://jamtangankanan.blogspot.com/
http://www.souvenirnikahku.com/
http://xamthonecentral.com/
http://www.jualsextoys.com
http://cupu.web.id
http://seoweblog.net
http://corsva.com
http://yudinet.com/
http://bookingbaliaccommodation.com/
http://seminyakbalivillarental.com/
http://www.ubudbalivillasrental.com/
http://www.balivillaexotic.com/
http://thechristinbalivilla.com/
http://www.pusatjamoriginal.com/
http://citralandpekanbaru.net78.net/
http://www.jeparacrafters.com/
http://www.cantikherbal.com/
http://www.kopiluwakbandung.com/
http://tki-mandiri.com/
http://citraindah.biz/
http://www.kostumbadut.info/
http://www.rotankita.com/
http://www.souvenirku182.blogspot.com/
http://keishaflorist.com/
http://jaringaniklan.om-onny.com/
http://www.om-onny.com/2012/01/agen-haji-dan-umroh.html
http://www.om-onny.com/2012/02/cara-cepat-meninggikan-badan.html
http://www.om-onny.com/2012/02/cara-cepat-mengencangkan-payudara.html
http://www.om-onny.com/2012/02/cara-mengatasi-insomnia-sulit-tidur.html
http://www.om-onny.com/2011/11/4-cara-mengecilkan-perut-buncit.html
http://www.om-onny.com/2012/01/tips-menghilangkan-stress-di-pagi-hari.html
http://www.om-onny.com/2012/01/4-tips-agar-awet-muda.html
http://www.om-onny.com/2012/02/cara-menambah-berat-badan.html
http://www.om-onny.com/2012/02/tips-cara-memilih-dan-memakai-jilbab.html
http://www.om-onny.com/2012/02/cara-cek-server-hosting-yang-lagi-down.html
http://www.om-onny.com/2012/01/cara-membuka-indera-keenam.html
http://www.om-onny.com/2011/04/buka-aura-dan-cakra-tenaga-dalam.html
http://www.om-onny.com/2012/02/tips-cara-memilih-dan-memakai-jilbab.html
http://seo.corsva.com
http://seojek.edublogs.org/
http://tasgrosir-brandedmurah.com/
http://upin.blogetery.com/
http://www.symbian-kreatif.co.cc/
http://upin.blog.com/
http://release.pressku.com/
http://cupu.web.id/tablet-android-honeycomb-terbaik-murah/
http://cupu.web.id/hotel-murah-di-yogyakarta-klikhotel-com/
http://www.seoweblog.net/hotel-murah-di-semarang-klikhotel-com/

