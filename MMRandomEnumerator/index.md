---
layout: page
title: MMRandomEnumerator
---



Credit where credit is due: This is MichaelMeer's original MMRandomEnumerator class

MMRandomEnumerator.h

    
//  MMRandomEnumerator.h
//  XQuizIt

//  Created by Michael Meer on Thu Oct 31 2002.
//  Copyright (c) 2002 Michael Meer. All rights reserved.

#import <Cocoa/Cocoa.h>

@interface MMRandomEnumerator: NSObject
{
	NSMutableArray *array;
	int currentIndex;
}

#pragma mark ��� INITIALIZATION ���

+ ( id ) initWithArray: ( NSArray * ) newArray;
- ( id ) nextObject;
- ( int ) count;

#pragma mark ��� ACCESSORS ���

- ( void ) setArray: ( NSMutableArray * ) newArray;
- ( NSArray * ) array;
- ( int ) currentIndex;

@end


MMRandomEnumerator.m:

    
//  MMRandomEnumerator.m
//  Shuffle the order of table entries or other array

//  Created by Michael Meer on Thu Oct 31 2002.
//  Copyright (c) 2002 Michael Meer. All rights reserved.

#import "MMRandomEnumerator.h"

@implementation MMRandomEnumerator

#pragma mark ��� INITIALIZATION ���

- ( id ) init
{
	if ( self = [ super init ] )
	{
		array =  [ [ NSMutableArray alloc ] init ];
		currentIndex = 0;
	}
	return self;
}

+ ( id ) initWithArray: ( NSArray * ) newArray
{
	MMRandomEnumerator *enumerator;
	NSMutableArray *newArrayCopy;
	NSMutableArray *resultArray;
	int arrayCount;
	int index;
	
	arrayCount = [ newArray count ];
	enumerator = [ [ MMRandomEnumerator alloc ] init ];
	newArrayCopy = [ NSMutableArray arrayWithArray: newArray ];
	resultArray = [ NSMutableArray arrayWithCapacity: arrayCount ];
	
	while ( [ resultArray count ] < arrayCount )
	{
		index = random( ) % [ newArrayCopy count ];
		[ resultArray addObject: [ newArrayCopy objectAtIndex: index ] ];
		[ newArrayCopy removeObjectAtIndex: index ];
	}
	
	[ enumerator setArray: resultArray ];
	
	return [ enumerator autorelease ];
}

- ( void ) dealloc
{
	[ array release ];
	[ super dealloc ];
}

- ( id ) nextObject
{
	if ( currentIndex >= [ array count ] )
	{
		return nil;
	}
	else
	{
		return [ array objectAtIndex: currentIndex++ ];
	}
}

- ( int ) count
{
	return [ array count ];
}

#pragma mark ��� ACCESSORS ���

- ( void ) setArray: ( NSMutableArray * ) newArray
{
	[ array autorelease ];
	array = [ newArray retain ];
}

- ( NSArray * ) array
{
	return array;
}

- ( int ) currentIndex
{
	return currentIndex;
}

@end

