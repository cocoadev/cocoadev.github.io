---
layout: page
title: NSNetworkSettings
---

Undocumented Foundation class that looks pretty useful for HTTP proxy configuration, including but not limited to the evaluation of PAC files.

    
@interface NSNetworkSettings : NSObject
{
    NSNetworkSettingsInternal *_internal;
}

+ (id)sharedNetworkSettings;
- (id)init;
- (void)dealloc;
- (id)proxyPropertiesForURL:(id)fp8;
- (BOOL)isProxyNeededForURL:(id)fp8;
- (void)setProxyPropertiesForURL:(id)fp8 onStream:(struct __CFReadStream *)fp12;
- (BOOL)connectedToInternet:(BOOL)fp8;

@end


Does anyone have any experience using this class?
----
It does not evaluate PAC files

