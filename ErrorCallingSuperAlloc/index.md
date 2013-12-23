---
layout: page
title: ErrorCallingSuperAlloc
---

While trying to code a ClassCluster, I came across a problem when calling [super alloc] and [super allocWithZone:]

Here's a small test case:

    % cat > x.m 

 #import <Foundation/Foundation.h> 
  
  
 @interface NSString (XXX) 
 @end 
  
  
 @implementation NSString (XXX) 
  
 + (id) alloc
 { 
    return( [super alloc]); 
 } 

 @end 

% gcc -c x.m
x.m:12: illegal expression, found `unknown'
cpp-precomp: warning: errors during smart preprocessing, retrying in basic mode
x.m: In function `+[NSString(XXX) alloc]':
x.m:12: dereferencing pointer to incomplete type

% gcc --version
gcc (GCC) 3.1 20020420 (prerelease)
Copyright (C) 2002 Free Software Foundation, Inc.



Any ideas as to what's going on there?

-- JensBaumeister

----

J,

You have got some funky character before the "return" statement. Try erasing everything before the "return" statement and recompiling. Also I don't think you want to be calling super in a constructor method. Class methods should do the following to allocate a new uninitialized instance of the class.

    

+ (id) alloc 
{ 
    return [NSString alloc]; 
} 



--zootbobbalu

I just looked at the raw data that exists in your source and for some reason you have the "unsigned char" value of 202 stuck between spaces (ASCII character value 32).

----

That gremlin character got in there somewhere during posting, but it's not in the original code. I tried re-typing the whole thing and still got the error.

As for why I'm calling [super alloc], look at the code snippets for ClassClusters - that should clear that up. :-)

-- JensBaumeister

