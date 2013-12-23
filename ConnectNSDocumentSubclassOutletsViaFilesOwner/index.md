---
layout: page
title: ConnectNSDocumentSubclassOutletsViaFilesOwner
---



I'm writing a document-based application using NSDocument as my "document controller" and I'm experiencing some weird things.
My NSDocument subclass contains an instance variable named "transactions", containing objects of class "Transaction", the document
view is NSTableView-based and works with the transaction list object, instance named "transactions".

Whenever I try to save my document, a _different_ instance, other  than the one containing the "transactions" object which has been modified through my UI, gets called. 
I see that when I start my application two completely different instances get created.

Whenever I try to save my NSDocument, the instance with the "transactions" object that hasn't been modified receives the dataRepresentationOfType message. This is the wrong one

----
This is consistent with adding an instance to the nib, I think. Check for that. BTW, what kind of object is your "list"? An array? Just making sure you've implemented NSCoding or NSKeyValueCoding. �Brent

----
What should I do: I can't really remove the instance in the nib, since I want to connect the UI in InterfaceBuilder to some outlets I've got.
Yes, the class implements NSCoding, but that was never really the problem.

----

Remove the instance and connect the objects to FilesOwner

----

**Another way that this can play out**

I am having difficulty saving data from my Document based application.
The app is working ok, and I would like only to save one NSString object.
I have added the encodeWithCoder and initWithCoder method within my model class:

    
-(void)encodeWithCoder:(NSCoder *)coder
{
NSLog (@"encodeWithCoder PC1LLFI is: %@", [self PC1LLFI]);
[coder encodeObject:[self PC1LLFI] forKey:@"PC1LLFI"];
}

- (id)initWithCoder:(NSCoder *)coder
{
[super init];
[self setPC1LLFI:[coder decodeObjectForKey:@"PC1LLFI"]];
NSLog (@"initWithCoder PC1LLFI is: %@", [self PC1LLFI]);
return self;
}


and edited the loadDataRepresentationOfType and DataRepresentationOfType within my Document class:


    
- (NSData *)dataRepresentationOfType:(NSString *)aType
{
	NSLog (@"lLFileInfo to archive is : %@", lLFileInfo);
    return [NSKeyedArchiver archivedDataWithRootObject:lLFileInfo];
}

- (BOOL)loadDataRepresentation:(NSData *)data ofType:(NSString *)aType
{
    NSLog (@"About to read data of type %@", aType);
	LLFileInfo *loadedName = [NSKeyedUnarchiver unarchiveObjectWithData:data];
	NSLog (@"loadedName from archive is : %@", loadedName);
	if (loadedName == nil) {
	return NO;
	} else {
	[self setPC1: [loadedName PC1LLFI]];
	return YES;}

The method is fine (I�m using it elsewhere), but I keep getting �NULL� at every NSLog stage when I try to save, and I�m told the document cannot be opened.  I also tried implementing the dataOfType method but I get the same result.  Am I going about this the wrong way?  Many thanks in advance.

I appreciate this should be an easy opperation.  Trouble is, I can successfully call the variable I am trying to archive from within my Document class before and after the save attempt.
Using the exact same method within the encoding method just seems to retun Nil.  Rats!

----

Sounds like you probably have two NSDocument subclass instances, one which is doing the work, and the other which is getting invoked when you save. Did you do something like instantiate your subclass in your MyDocument.nib? This would create a second instance, and thus confusion. Try logging the pointer value of     self both in the cases where it works and where it doesn't, and see if they're different.

----

Aaha! I did instantiate MyDocument in the nib file. OK, I don't think I have a clear understanding of the Document architecture.  I'm now wondering where myDocument interface connections are made.  Everything seemed to beworking fine before I tried switching to a Document based App.  Many thanks for your comment though, I now have a route to investigate. :)

----

File's Owner is your document subclass instance, as it's the one which actually loads (and thus owns) the nib.

----

SOLVED!! My app is now archiving happily.

Remember kids, don't instantiate your NSDocument subClass from within your nib file!

----

See also the related topics FilesOwner and OutletsToFilesOwner


----

How did you solve the problem? I am having the same problem. Where do you instantiate MyDocument in the nib file?
----

