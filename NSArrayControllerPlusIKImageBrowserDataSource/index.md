---
layout: page
title: NSArrayControllerPlusIKImageBrowserDataSource
---

Here is a category that makes an NSArrayController a valid IKImageBrowserDataSource. Additionally, the example implementations of imageBrowser:moveItemsAtIndexes:toIndex: I�ve seen are buggy. The one below seems to work flawlessly.

Note that if you have the array controller�s arrangedObjects bound as the content of the IKImageBrowserView, you still need to set the array controller as the IKImageBrowserView�s dataSource and to implement imageBrowser:moveItemsAtIndexes:toIndex to get rearranging to work. Hopefully Apple will fix that�  � BryanWoods

    
// NSArrayController+IKImageBrowserDataSource.m

@implementation NSArrayController (IKImageBrowserDataSource)

- (NSUInteger) numberOfItemsInImageBrowser:(IKImageBrowserView *)browser
{
	return self arrangedObjects] count];
}

- (id) imageBrowser:([[IKImageBrowserView *)browser itemAtIndex:(NSUInteger)index
{
	return self arrangedObjects] objectAtIndex:index];
}

- (void) imageBrowser:([[IKImageBrowserView *)browser removeItemsAtIndexes:(NSIndexSet *)indexes
{
	return [self removeObjectsAtArrangedObjectIndexes:indexes];
}

- (BOOL) imageBrowser:(IKImageBrowserView *)browser moveItemsAtIndexes:(NSIndexSet *)indexes toIndex:(NSUInteger)index
{
	__block NSUInteger destination = index;
	[indexes enumerateIndexesUsingBlock:^(NSUInteger idx, BOOL * stop) {
			if(idx < index)
				--destination;
			else
				*stop = YES;
		}];
	
	NSArray * items = self arrangedObjects] objectsAtIndexes:indexes];

	[self willChangeValueForKey:@"arrangedObjects"];
	[self removeObjectsAtArrangedObjectIndexes:indexes];

	[items enumerateObjectsWithOptions:[[NSEnumerationReverse usingBlock:^(id item, NSUInteger idx, BOOL * stop) {
			[self insertObject:item atArrangedObjectIndex:destination];
		}];
	[self didChangeValueForKey:@"arrangedObjects"];

	[self performSelectorOnMainThread:@selector(setSelectionIndex:) withObject:(id)destination waitUntilDone:NO];

	return YES;
}


@end

