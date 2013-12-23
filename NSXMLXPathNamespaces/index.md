---
layout: page
title: NSXMLXPathNamespaces
---



I'm developing a Cocoa application in which the data model is heavily based on an XML format.  I'd like to use the XPath functionality of NSXML as it will make querying the underlying data much easier, but am not sure how to bind a particular namespace prefix to a URI.  For example, the query     ./body//p depends on the namespace of the element it is executed against.  I'd like to control how namespace prefixes are bound in the application code to handle cases where the document binds namespaces differently.

I know other libraries use a dictionary mapping prefixes to namespace URIs.  Something along the lines of:

    
  NSDictionary *bindings = [NSDictionary withObject:@"http://www.w3.org/1999/xhtml" forKey:@"xh"];
  NSArray *nodes = [xmlDoc nodesForXPath:@"./xh:body//xh:p" withNamespaces:bindings error:&err];


The only way I can see to achieve this with the NSXML API is building a formatted XQuery string, but it seems like there should be a better way.

----

Replying to myself, I've written a Category to add the functionality for XQuery queries, which may be slower than XPath but seems to get the job done.  I'm posting the code here in case anyone else finds it useful.

    

@interface NSXMLNode (QueryExtensions)
- (NSArray *) objectsForXQuery:(NSString *)query constants:(NSDictionary *)constants namespaces:(NSDictionary *)bindings error:(NSError **)error
@end

@implementation NSXMLNode (QueryExtensions)

- (NSArray *) objectsForXQuery:(NSString *)query constants:(NSDictionary *)constants namespaces:(NSDictionary *)bindings error:(NSError **)error
{
	if (bindings != nil) {
		NSString *key;
		NSMutableString *compiledQuery = [[NSMutableString alloc] init];
		NSEnumerator *keys = [bindings keyEnumerator];
		while (key = [keys nextObject]) {
			[compiledQuery appendFormat:@"declare namespace %@ = \"%@\";\n", key, [bindings objectForKey:key]];
		}
		[compiledQuery appendString:query];
		query = compiledQuery;
	}
	
	return [self objectsForXQuery:query constants:constants error:error];
}

@end


