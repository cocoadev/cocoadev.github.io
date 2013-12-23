---
layout: page
title: BindingsWithDistributedObjects
---



Hello World,

I'm working on a CocoaFramework which will provide a networking layer for CoreData using DistributedObjects. Given the overwhelming advantages of the bindings architecture (especially when used with CoreData) it would be nice if you could bind to/observe distant objects transparently - which is currently unsupported. [Mac OS X 10.4]

I spoke with the author of the following post and he apparently got all of this working, which gives me hope.

http://lists.apple.com/archives/cocoa-dev/2005/Jun/msg01293.html

I'll be updating this page regularly with progress reports, questions, and expressions of anxiety.

Any help will be appreciated.

EliotSimcoe

----

When a client connects to a server hosted by an NSConnection which is vending an objcet, an NSDistantObject instance is created on both the server and the client to control message flow across the line. The client side NSDistantObject forwards its method invocations to the server side NSDistantObject, which forwards them to the actual object which performs the action and returns the results (which are then sent back to the client). I think this is why bindings and observing don't work. There is no relay from the real object back to the client side NSDistantObject when changes are made. Additionally the -addObserver:forKeyPath:options:context: method cannot be forwarded using DistributedObjects because of the void * context parameter. By keeping the observation info and binding info local to the client, we can circumvent this conundrum.

Something along the following lines should work:

* Add +(void)poseAs: to NSProxy.
* Subclass NSDistantObject as MyDistantObject.
* Implement MyDistantObject+NSKeyValueObserving.
* Implement MyDistantObject+NSKeyValueBindingCreation.
* Add -(NSSet *)distantObjectProxies to NSObject.
* Subclass NSObject as MyObject.
* Implement MyObject+NSKeyValueObserving.


MyDistantObject will pose as NSDistantObject, so it cannot have instance variables. It must implement the following methods of the NSKeyValueBindingCreation and NSKeyValueObserving informal protocols:

* - (void)bind:(NSString *)binding toObject:(id)observableController withKeyPath:(NSString *)keyPath options:(NSDictionary *)options;
* - (void)unbind:(NSString *)binding;
* - (NSDictionary *)infoForBinding:(NSString *)binding;

* - (void)addObserver:(NSObject *)anObserver forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(void *)context;
* - (void)removeObserver:(NSObject *)anObserver forKeyPath:(NSString *)keyPath;

* - (void)willChangeValueForKey:(NSString *)key;
* - (void)didChangeValueForKey:(NSString *)key;
* - (void)willChange:(NSKeyValueChange)change valuesAtIndexes:(NSIndexSet *)indexes forKey:(NSString *)key;
* - (void)didChange:(NSKeyValueChange)change valuesAtIndexes:(NSIndexSet *)indexes forKey:(NSString *)key;
* - (void)willChangeValueForKey:(NSString *)key withSetMutation:(NSKeyValueSetMutationKind)mutationKind usingObjects:(NSSet *)objects;
* - (void)didChangeValueForKey:(NSString *)key withSetMutation:(NSKeyValueSetMutationKind)mutationKind usingObjects:(NSSet *)objects;

* - (void *)observationInfo;
* - (void)setObservationInfo:(void *)value;


MyObject will pose as NSObject, so it cannot have instance variables. It must implement the following methods of the NSKeyValueObserving informal protocol:

* - (void)willChangeValueForKey:(NSString *)key;
* - (void)didChangeValueForKey:(NSString *)key;
* - (void)willChange:(NSKeyValueChange)change valuesAtIndexes:(NSIndexSet *)indexes forKey:(NSString *)key;
* - (void)didChange:(NSKeyValueChange)change valuesAtIndexes:(NSIndexSet *)indexes forKey:(NSString *)key;
* - (void)willChangeValueForKey:(NSString *)key withSetMutation:(NSKeyValueSetMutationKind)mutationKind usingObjects:(NSSet *)objects;
* - (void)didChangeValueForKey:(NSString *)key withSetMutation:(NSKeyValueSetMutationKind)mutationKind usingObjects:(NSSet *)objects;


The server side real object needs to forward change notifications to the client side MyDistantObject. The client will run a server which will provide proxies to the client side MyDistantObject for the server side real object. Using this mechanism, the real object can notify the client side MyDistantObject when an instance variable has changed in addition to notifying itself. Since the client side MyDistantObject instance implements the methods in the KeyValueObserving protocol, they will not be forwarded back to the real object. (which would create an infinite loop).

----

Chris Kane's point is that you cannot pass a void* over the wire.  You may have to introduce a requirement that the context parameter be an NSString.  Unfortunately, you cannot control what the framework uses for context, so it's going to be a bad idea to bind an Apple-created view or controller to a remote model object.  (In my own code, I've often used a selector - not an object - as the context.)

We can keep the binding and observation info on the client so it never has to cross the wire. Then we just use that information to update the observers when a change is made.

