---
layout: page
title: Alloc+Init=New
---

\\%\\%BEGINENTRY\\%\\%TipsAndTricks\\%\\%ENDENTRY\\%\\%

A nice little code clean up with Xcode 5 and higher. When you wish to create an object, but it doesn't need a special init with parameters and such, instead of:

  UIView *view = [[UIView alloc] init];

you can use

   UIView *view = [UIView new];

It's a small thing, but more readable.

