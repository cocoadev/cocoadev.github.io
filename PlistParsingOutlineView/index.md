---
layout: page
title: PlistParsingOutlineView
---

Before I go and reinvent the wheel, does anyone have any code to parse a .plist file and display it in a NSOutlineView? I need to do something similar to PropertyListEditor.
----
Here is something to start with... I did not write it to the end, because I am almost as lazy as you! :)
    
void add_entry (id src, NSMutableArray* dst, NSString* name)
{
   NSMutableDictionary* entry = [NSMutableDictionary dictionaryWithObjectsAndKeys:
      name, @"label",
      src, @"object",
      nil];

   if([src isKindOfClass:[NSArray class]])
   {
      NSMutableArray* children = [NSMutableArray array];
      for(unsigned i = 0; i != [src count]; ++i)
         add_entry([src objectAtIndex:i], children, [NSString stringWithFormat:@"%ud", i]);
      [entry setObject:children forKey:@"children"];
   }
   else if([src isKindOfClass:[NSDictionary class]])
   {
      NSMutableArray* children = [NSMutableArray array];
      NSEnumerator* enumerator = [src keyEnumerator];
      while(id key = [enumerator nextObject])
         add_entry([src objectForKey:key], children, key);
      [entry setObject:children forKey:@"children"];
   }
   else ...
   {
      ...
   }
   [dst addObject:entry];
}

// load data in init
- (id)initWithPath:(NSString*)aPath
{
   if(self = [super init])
   {
      NSDictionary* tmp = [NSDictionary dictionaryWithContentsOfFile:aPath];
      plist = [[NSMutableArray alloc] init];
      add_entry(tmp, plist, @"root");
   }
   return self;
}

// data source methods
- (int)outlineView:(NSOutlineView*)outlineView numberOfChildrenOfItem:(id)item
{
   NSArray* array = item ? [item objectForKey:@"children"] : plist;
   return array ? [array count] : 0;
}

- (BOOL)outlineView:(NSOutlineView*)outlineView isItemExpandable:(id)item
{
   return [item objectForKey:@"children"] != nil;
}

- (id)outlineView:(NSOutlineView*)outlineView child:(int)index ofItem:(id)item
{
   NSArray* array = item ? [item objectForKey:@"children"] : plist;
   return [array objectAtIndex:index];
}

- (id)outlineView:(NSOutlineView*)outlineView objectValueForTableColumn:(NSTableColumn*)tableColumn byItem:(id)item
{
   return [item objectForKey:[tableColumn identifier]];
}

