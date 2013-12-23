---
layout: page
title: DPAssert
---

DPAssert is an assertion macro identical to NSAssert, except it can handle any number of arguments to its format string. There's no need to use variants like NSAssert2 to use more arguments.

    
@class NSAssertionHandler, NSString;

/* Asserts for Objective-C that accepts unlimited number of arguments */
#if !defined(NS_BLOCK_ASSERTIONS)

#if !defined(DPAssert)
/*
 * The usage of this macro is the same as with NSAssert, exept you don't need DPAssert1, DPAssert2, etc.
 * This macro accepts any number of arguments passed to the description.
 */
#define DPAssert(condition, desc...)\
do {\
	if (!(condition)) {\
		[[NSAssertionHandler currentHandler] handleFailureInMethod:_cmd\
															object:self\
															  file:[NSString stringWithUTF8String:__FILE__] \
														lineNumber:__LINE__\
													   description:desc];\
	}\
} while(0)
#endif

#if !defined(DPCAssert)
/*
 * The usage of this macro is the same as with NSAssert, exept you don't need DPAssert1, DPAssert2, etc.
 * This macro accepts any number of arguments passed to the description.
 */
#define DPCAssert(condition, desc...)\
do {\
	if (!(condition)) {\
		[[NSAssertionHandler currentHandler] handleFailureInFunction:[NSString stringWithUTF8String:__PRETTY_FUNCTION__]\
																file:[NSString stringWithUTF8String:__FILE__] \
														  lineNumber:__LINE__\
														 description:desc];\
	}\
} while(0)
#endif

#endif	// !defined(NS_BLOCK_ASSERTIONS)


-OfriWolfus

