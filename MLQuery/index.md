---
layout: page
title: MLQuery
---



Part of the iPhone MusicLibraryFramework. Without any filter predicates set, MLQuery returns all tracks.

    
MLQuery* query = [[MLQuery alloc] init];
int numberOfTracks = [query countOfEntities];
int i;

for ( i = 0; i < numberOfTracks; ++i )
{
  MLTrack* track = [query entityAtIndex:i];
  NSLog(@"Artist: %@, Title: %@, file path: %@", [track artist], [track title], [track path]);
}


<code>- (id)init;</code>

<code>- (int)countOfEntities;</code>

<code>- (MLTrack*)entityAtIndex:(int)idx;</code>

