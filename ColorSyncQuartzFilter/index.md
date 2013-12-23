---
layout: page
title: ColorSyncQuartzFilter
---

Hello everyone.

It would be awesome if we could process images using the new Quartz Filters under the ColorSync pane in the print panel in Panther, but there doesn't seem to be any documentation on it. Does anyone know how to access this functionality programmatically? I would like to provide these filters in a graphics application I am writing.

Thanks,
-- Eliot Simcoe
-- esimcoe at mac dot com

----

The filters themselves are apparently stored in property list format at /System/Library/Filters/ with .qfilter extensions. I have no idea what to do with them though.

