---
layout: page
title: HandyIPhoneCodeSnippets
---



slerp has a whole mess of handy snippets: http://phonedev.tumblr.com

----

**Take a screen shot**

Thanks to Erica Sadun, I believe.

    
@implementation UIView (Snapshot)

- (void)saveSnapshot
{
	CGRect bounds;
	int err = 0, i;
	CGImageRef image = nil;
	char path[ 32 ];
	CGContextRef pdfContext = nil;
	struct stat sb;
	CFStringRef string = nil;
	CFURLRef url = nil;
	
	bounds = [self bounds];
	
	for ( i = 0; i < 100; ++i )
	{
		snprintf( path, sizeof(path), "/tmp/snapshot_%d.pdf", i );
		if ( stat( path, &sb ) == -1 ) break;
	}
	
	if ( ! ( string = CFStringCreateWithCString( nil, path, kCFStringEncodingASCII ) ) ) err = 1;
	if ( ! err && ! ( image = [self createSnapshotWithRect: bounds] ) ) err = 1;
	if ( ! err && ! ( url = CFURLCreateWithFileSystemPath( nil, string, kCFURLPOSIXPathStyle, 0 ) ) ) err = 1;
	if ( ! err && ! ( pdfContext = CGPDFContextCreateWithURL( url, &bounds, nil ) ) ) err = 1;

	if ( ! err ) {
		CGContextBeginPage( pdfContext, &bounds );
		CGContextDrawImage( pdfContext, bounds, image );
		CGContextEndPage( pdfContext );
		CGContextFlush( pdfContext );
	}
	
	if ( pdfContext ) CGContextRelease( pdfContext );
	if ( url ) CFRelease( url );
	if ( image ) CGImageRelease( image );
	if ( string ) CFRelease( string );
}

@end


----

**Subclassing UIKit**

To see what the rest of UIKit is expecting from your subclass, you can log calls to respondsToSelector: and the like.

    
- (BOOL)respondsToSelector:(SEL)selector
{
  NSLog(@"respondsToSelector: %s", selector);
  return [super respondsToSelector:aSelector];
}

- (NSMethodSignature*)methodSignatureForSelector:(SEL)selector
{
  NSLog(@"methodSignatureForSelector: %s", selector);
  return [super methodSignatureForSelector:selector];
}

- (void)forwardInvocation:(NSInvocation*)invocation
{
  NSLog(@"forwardInvocation: %s", [invocation selector]);
  [super forwardInvocation:anInvocation];
}


----

If you use a function that returns a float or a double, you'll get a linker error on the function objc_msgSend_fpret. Here's a standin until the toolchain is fixed:

    

double objc_msgSend_fpret(id self, SEL op, ...) {
	Method method = class_getInstanceMethod(self->isa, op);
	int numArgs = method_getNumberOfArguments(method);
	
	if(numArgs == 2) {
		double (*imp)(id, SEL);
		imp = (double (*)(id, SEL))method->method_imp;
		return imp(self, op);
	} else if(numArgs == 3) {
		// FIXME: this code assumes the 3rd arg is 4 bytes
		va_list ap;
		va_start(ap, op);
		double (*imp)(id, SEL, void *);
		imp = (double (*)(id, SEL, void *))method->method_imp;
		return imp(self, op, va_arg(ap, void *));
	}
	
	// FIXME: need to work with multiple arguments/types
	fprintf(stderr, "ERROR: objc_msgSend_fpret, called on <%s %p> with selector %s, had to return 0.0\n", object_getClassName(self), self, sel_getName(op));
	return 0.0;	
}


**Update:** http://www.tuaw.com/2007/09/04/iphone-coding-using-the-slider/ says that this is no longer necessary as of the v.20 toolchain.

