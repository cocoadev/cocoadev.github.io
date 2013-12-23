---
layout: page
title: BOMArchiveHelper
---

BOMArchiveHelper is a core service that provides the mechanism behind the "Create Archive�" contextual menu in the Finder. It was introduced in Panther. Note that there are other libraries for zip compression available in OS X, and that BOMArchiveHelper is not well documented. The zip archives it produces have a proprietary method, similar to AppleDouble, for preserving resource forks and other HFS metadata. As of this writing only BOMArchiveHelper and StuffIt 9 support it, but the archives can be opened with any other unzip program to access only the data forks.

