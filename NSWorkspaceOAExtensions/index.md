---
layout: page
title: NSWorkspaceOAExtensions
---



A category on NSWorkspace that adds one very handy method:

    - (NSString *)fullPathForApplicationWithIdentifier:(NSString *)bundleIdentifier;

This lets you search by identifier for an app, which means the user can rename or move the app and you'll still find it. It uses LaunchServices.

