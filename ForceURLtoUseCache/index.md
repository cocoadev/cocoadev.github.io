---
layout: page
title: ForceURLtoUseCache
---

I use WebView to load page, and some pages have cache-control dirrectives (pragma no-cache, etc.)
WebView follows this dirrectives, so when I load them, there are no access to data via

    
- (id)webView:(WebView *)sender identifierForInitialRequest:(NSURLRequest *)request fromDataSource:(WebDataSource *)dataSource
{
        return (request);
}
-(void)webView:(WebView *)sender resource:(id)identifier didFinishLoadingFromDataSource:(WebDataSource *)dataSource
{
        NSCachedURLResponse *cResponse = [ [NSURLCache sharedURLCache] cachedResponseForRequest:identifier];
}


cResponse is nil��

what can I do to access data of resource for loaded page in case of no-cache pragma?
Is there any way to force storing cache for such pages?

