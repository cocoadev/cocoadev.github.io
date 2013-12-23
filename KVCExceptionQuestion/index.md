---
layout: page
title: KVCExceptionQuestion
---

I am trying to create a dictionary-like, bindings enabled data structure. It has a custom to-many relationship key named "entries" which I implemented using this custom code:

    
- (unsigned int) countOfEntries {
	return [val_entries count];
}

- (LclEntry*) objectInEntriesAtIndex:(unsigned int) idx {
	return [val_entries objectAtIndex:idx];
}

- (void) insertObject:(LclEntry*) entry inEntriesAtIndex:(unsigned int) idx {
	[entry assignEntrySet:self];
	[val_entries insertObject:entry atIndex:idx];
	[keys addObject:[entry key]];
}

- (void) removeObjectInEntriesAtIndex:(unsigned int) idx {
	LclEntry* entry = [val_entries objectAtIndex:idx];
	[entry assignEntrySet:nil];
	[keys removeObject:[entry key]];
	[val_entries removeObjectAtIndex:idx];
}


When I (or the bindings layer) try to invoke methods from the array of     mutableArrayValueForKey:@"entries", this exception is thrown:

    
[<LclEntrySet 0x3467e0> setValue:forUndefinedKey:]: this class is not key value coding-compliant for the key entries.


Am I doing something wrong? -- l0ne aka EmanueleVulcano

