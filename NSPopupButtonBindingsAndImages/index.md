---
layout: page
title: NSPopupButtonBindingsAndImages
---

I'd like to use cocoa bindings but to have nice icons in my NSPopupButtons. Is it possible?

Currently, i think the better option is to sublass NSPopupButton and to override 
- (void)bind:(NSString *)bindingName	toObject:(id)observableObject withKeyPath:(NSString *)observableKeyPath options:(NSDictionary *)options

But there is maybe a better solution.

KDM

