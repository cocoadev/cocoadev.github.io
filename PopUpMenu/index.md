---
layout: page
title: PopUpMenu
---

NSView's have a method to associate an event with a context menu (    menuForEvent:). Here's an example of how you can use this method: --zootbobbalu

*some custom view*
    
- (void)copyToPasteboard:(id)sender {NSLog(@"copyToPasteboard");}
- (void)deleteFile:(id)sender {NSLog(@"deleteFile");}
- (void)openInTextEdit:(id)sender {NSLog(@"openInTextEdit");}
- (void)showInFinder:(id)sender {NSLog(@"showInFinder");}

- (NSMenu *)menuForRightMouseDownEvent {
    NSDictionary *editInfo, *info;
    editInfo = [NSMenu infoWithTitle:@"Edit" items:@"Copy to Pasteboard/copyToPasteboard:", 
                                                    @"Delete File/deleteFile:", nil];
    info = [NSMenu infoWithTitle:@"" items:editInfo, @"Open in TextEdit/openInTextEdit:", 
                                                    @"Show in Finder/showInFinder:", nil];
    return [NSMenu popUpMenuWithInfo:info target:self];
}

- (NSMenu *)menuForEvent:(NSEvent *)theEvent {
    if ([theEvent type] == NSRightMouseDown) return [self menuForRightMouseDownEvent];
    return nil; 
}


*category addition to NSMenu to help create a quick PopUpMenu*
    
@interface NSMenu (NSMenu_PopUpAdditions)
+ (NSMenu *)popUpMenuWithInfo:(NSDictionary *)plist target:(id)target;
+ (NSDictionary *)infoWithTitle:(NSString *)title items:(id)item, ...;
@end NSMenu (NSMenu_PopUpAdditions)

@implementation NSMenu (NSMenu_PopUpAdditions)

+ (NSMenu *)popUpMenuWithInfo:(NSDictionary *)info target:(id)target {
    if (![info isKindOfClass:[NSDictionary class]]) return nil;
    NSString *title = [info objectForKey:@"title"];
    NSArray *items = [info objectForKey:@"items"];
    if (!title) title = @"";
    if (![items isKindOfClass:[NSArray class]]) return nil;
    NSMenu *menu = [[[NSMenu alloc] initWithTitle:title] autorelease];
    NSEnumerator *itemEnum = [items objectEnumerator]; id item;
    while (item = [itemEnum nextObject]) {
        if ([item isKindOfClass:[NSDictionary class]]) {
            NSMenu *submenu = [self popUpMenuWithInfo:item target:target];
            if (!submenu) continue; 
            NSMenuItem *subItem = [[[NSMenuItem alloc] init] autorelease];
            [subItem setTitle:[submenu title]];
            [subItem setSubmenu:submenu];
            [menu addItem:subItem];
        } else if ([item isKindOfClass:[NSString class]]) {
            NSArray *comps = [item componentsSeparatedByString:@"/"];
            if ([comps count] < 2) continue;
            NSMenuItem *menuItem = [menu addItemWithTitle:[comps objectAtIndex:0] 
                                        action:NSSelectorFromString([comps lastObject])
                                        keyEquivalent:@""];
            [menuItem setTarget:target];
        }
    }
    return menu;
}

+ (NSDictionary *)infoWithTitle:(NSString *)title items:(id)item, ... {
    va_list ap; 
    va_start(ap, item);
    id nextItem = nil;
    NSMutableDictionary *info = [NSMutableDictionary dictionary];
    [info setValue:title forKey:@"title"];
    NSMutableArray *items = [NSMutableArray array];
    if (item) [items addObject:item];
    while (nextItem = va_arg(ap, id)) [items addObject:nextItem];
    [info setValue:items forKey:@"items"];
    va_end(ap); 
    return info;
}

@end

