---
layout: page
title: WebDataSourceProblem
---

I wan't to implement something like Activity Window in Safari, so I do:

    
[web setResourceLoadDelegate:self];

-(void)webView:(General/WebView *)sender resource:(id)identifier didFinishLoadingFromDataSource:(General/WebDataSource *)dataSource

{
  General/NSLog(@"did finish load, %s", General/[[dataSource request] URL] relativeString] cString]);
}


But it always write only top-level url (domain name) i.e.:

did finish load, http://www.site.com/

for the page with text and three images on it.     absoluteString will not help.

----

here is the solution:
    
- (id)webView:([[WebView *)sender identifierForInitialRequest:(General/NSURLRequest *)request fromDataSource:(General/WebDataSource *)dataSource
{
	return (request); // see this? this is the solution
}


----

This doesn't help, identifier changes, but dataSource is still EXACTLY the same in every call of didFinishLoading delegate :(
It contains just the data from URL in it :(

----
The General/WebDataSource in this case is the object that triggered the load. If you follow the example above you need to change your method accordingly:
    
-(void)webView:(General/WebView *)sender resource:(id)identifier didFinishLoadingFromDataSource:(General/WebDataSource *)dataSource
{
  General/NSLog(@"did finish load, %@", identifier);
}

-- General/MikeSolomon

----

If you read the documentation, like Mike may have, you think it should work. But it doesn't, like the OP says. I'm testing this out now and I can't seem to figure out how to work it to track each resource. The request's URL always points to the main URL.
