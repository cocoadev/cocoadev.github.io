---
layout: page
title: CustomCellsAndBindings
---

Okay, this is probably a stupid question, but i have created my own table cell class, drawn it how I want, and am ready to implement the data. I am using the cell's stringValue for the first string in the cell, and then I have others for other attributes of my CoreData entity. What I want to know is how do I get the information from bindings to be accessible to the class? I know for a normal cell I'd just bind it to my controller. How do I access that information inside of the cell's implementation code? Do I have to connect the controller to it in IB or do I have to somehow bind it? Again, probably a simple question, so please bear with me. --LoganCollins

----

*Can't you just bind the entire object, instead of a specific key? This *should* give you an NSManagedObject to work with, right? --JediKnil*

----

That just gives me a copyWithZone: exception. I connected the array controller to the column's value binding and chose arrangedObjects. I can't seem to get it to work, either. If anyone has any other suggestions, I would really appreciate them. I seem to have hit a wall.

----

The copyWithZone: exception is a result of the -setObjectValue: for your cell. -[NSCell setObjectValue:] makes a copy of what it gets as an object value, and NSManagedObjects aren't copyable - hence the exception....

What you can do to avoid this is override the -setObjectValue: method in your cell subclass to do something like replace the passed value with an [NSValue valueWithNonretainedObject:theObjectArgumentToSetValue]. NSValues are copyable, so you'll avoid the exception... To ensure that the object doesn't go away behind your back, you might want to also retain theObjectArgumentToSetValue before wrapping it in an NSValue, and release the old self objectValue] nonretainedObjectValue] whenever -setObjectValue: is called...

Now... a problem with using the object itself as a column value in an [[NSTableView is that you're likely displaying attributes of the object inside the cell, and so redisplay won't happen when attributes of the the object value change (because a bound table view automatically updates based on watching attributes associated with columns changing to trigger a re-display - unless the object address actually changes, then you won't see an update)... I haven't coded something up to get around this, but my first thought would be to have the cell observe changes on the attributes you're interested, then call self controlView] setNeedsDisplay:YES] when you get an update of an attribute that your particular cell subclass is watching...

If you find that this second-level cell-based observation of changes works in practice, I'd love to hear about it - so pls. post an update if you take this approach and test against it...

UPDATE:

I'd try as above, but I'm not sure it will work because, according to the documentation, the cell for a given column is re-used to display all of the rows shown inside that column - that's why I'm not sure it will work... You may find that [[NSTableView is just ugly for doing stuff that's not within the purview of what it does today for a column of values. I'm kinda' bummed out because a lot of bindings information is still opaque so if you want to do something slightly different from the usual, you might trip over all sorts of undocumented implementation details...

I'll hold back on criticism of the tree-based controller with an opaque class that represents nodes - that's just shoddy :) Apple's doing good for 90% of cases, but some of the opaqueness of the Controller hierarchy burns me a bit, personally :)

-- MrO

----

I think you'll be better off using -tableView:willDisplayCell:forTableColumn:row: to set additional custom information in your cell.  This is typically how it's done in the absence of bindings as well.

There's no reason you cannot grab the cell programmatically and bind it to whatever model you want, but that's unlikely to be what you really want.. as MrO said, there's typically only one cell instance used for an entire column.

I agree that it would be better if NSCell would not perform a copy in setObjectValue:.  I expect that you could bend Cocoa to your will by implementing the following in your NSCell subclass:

    
- (void)setObjectValue:(id <NSCopying>)object {
    id oldObjectValue = [self objectValue];
    if (object != oldObjectValue) {
        [object retain];
        [oldObjectValue release];
        [super setObjectValue:[NSValue valueWithNonretainedObject:object]];
    }
}

- (id)objectValue {
    return super objectValue] nonretainedObjectValue];
}


Now, despite the method declaration, you can pass it any object.

Another possibility might be to set up a value transformer.  Bind to the object value you're actually interested in, but use a value transformer to wrap/unwrap it in a copyable [[NSValue.  The thing is, I'm a little unclear on how exactly binding works for NSTableColumns.. it might be an array of values that gets wrapped rather than the objects themselves, which would be Bad (like, wouldn't work).  If this does work, it's a better option.  I don't like the idea that if NSCell accesses its internal storage directly, it will get different results than if it goes through -[NSCell objectValue].  Sounds Fishy.

