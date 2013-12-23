---
layout: page
title: HowToGetNSArrayControllerToRefreshCustomView
---

Here is how to refresh a custom view when the data from an NSArrayController is changed.

Here is what I did (and it works!)


*
In order to add a binding to your custom view that Interface Builder will recognize, you must create it by making an Interface Builder palette project.  This is the only way I have found to add a functional binding to a custom view so that you can have the custom view bound to an NSArrayController's data.  At least once you are done, you can reuse this custom view as a framework in other projects. (I forgot where I got a tutorial to do this, will try to find out and put a link here)
*
Add the following code to the custom view .h file
    
NSArray *dataSource;

*
Add the following code to the custom view .m file
    
+ (void)initialize {
    [self exposeBinding:@"dataSource"];

}

-(void)bind:(NSString *)binding
			toObject:(id)observableController
			withKeyPath:(NSString *)keyPath
			options:(NSDictionary *)options
{
	[observableController addObserver: self
					forKeyPath: @"arrangedObjects"
					options: NSKeyValueObservingOptionNew
					context: NULL];
	NSLog(@"I am observing the controller");
	[super	bind:binding
			toObject:observableController
			withKeyPath:keyPath
			options:options];
}

- (void) observeValueForKeyPath: (NSString *) keyPath
                       ofObject: (id) object
                         change: (NSDictionary *) change
                        context: (void *) context
{
	[self setNeedsDisplay:YES];
	NSLog(@"The controller just updated");

} // observeValueForKeyPath

*
Create a core data document based project and add an entity or two to store your data in.

*
Drag a tableView, plus and minus buttons, and your custom view onto the document window of the document nib in interface builder.

*
Drag an instance of NSArrayController onto the document nib.

*
Bind the columns of the tableView to the appropriate data.

*
Connect the plus button to the add: action of the NSArrayController and connect the minus button to the remove: action of the NSArrayController.

*
Connect the NSTableView to the fetch: action of the NSArrayController (so that when you modify any of the cells it sees itself as updated).

*
Using the newly created custom view binding (in this case "dataSource") bind this to the "arrangedObjects" of the NSArrayController.




----
I have created a core data document based application.  The interface includes a tableview, a plus button, and a minus button which are connected to an instantiated NSArrayController.  This setup allows the user to add, modify, and remove data.  In addition, I have created a specialized custom view with a binding to the data in core data.

The view can "see" the data and then draw it.  But it does not update the view immediately as the core data is modified.  I have included an IBAction in the custom view called redraw which basically just calls [self setNeedsDisplay:YES].  A button on the window is then attached to this action and will update the view when the user presses on it.  This is obviously less than ideal.  I would like for the NSArrayController to somehow refresh the custom view every time there is a change in the core data.  Or I would like the custom view to refresh itself every time the core data changes.  Any ideas about how to get this to happen?

----

You need to go back to the documentation for Cocoa Bindings, Core Data, and NSArrayController. You need to tell the array controller to -fetch: when the contents it represents changes. This is not automatic because you don't always necessarily want every NS*Controller to update its content set just because something elsewhere changed (consider a "report view" that displays a temporary report of objects that match a certain condition ... you may want a report before a change and another after the change for comparison ...). 

If you have a button directly wired to the controller's -add: method, you'll probably want to just create your own -add: method, which calls the array controller's -add: and its -fetch: in sequence. It's rare I ever have a button wired directly to a controller's -add: method because I usually want to do extra stuff (such as an immediate fetch).

----
Thank you for replying to my question.  I appreciate your help.  I don't think -fetch is the answer.  Currently the tableView updates automatically whenever a value in a cell is edited or when the add or remove buttons are pressed.  That is not the problem.  Just to test your theory, though, I connected the refresh button to the -fetch action of the NSArrayController.  It did not update the custom view when pressed.

My question is how to get the custom view to call setNeedsDisplay:YES whenever there is a change in the data.  The data is getting into the custom view just fine, but the user cannot see it, because the view has not updated itself.  I thought that perhaps I could register the custom view as an observer using the "addObserver: forKeyPath: options: context:" and "observerValueForKeyPath: ofObject: change: context" methods.  But when I add these two methods to my custom view, changes in the data do not trigger the "observerValueForKeyPath..." method.

----

See HowToAskQuestions -- if this is related directly to your own custom view code, you'll need to post the relevant code.
----
I am not sure that using those methods mentioned above are what I should do, which is why I am asking how someone would get a custom view to recognize when a change was made in core data (and therefore in the NSArrayController that stores them) and then to call setNeedsDisplay:YES for the custom view.  Because I do not know whether using these two methods is the way to go, I am asking the question first before I post code.  If someone says, "Yes!  Those two methods are what you should use.  It should work.  How are you calling them?"  Then I would post the code.  But I am not even sure that that is how I should be proceding.  Does anyone have an idea about how to referesh a custom view when the NSArrayController gets new data?  As I mentioned above, the tableView (and probably the custom view) are getting the data immediately as it changes through bindings.  The question is, how do I get the custom view to refresh with that new information?

----

The answer is "it depends". If you are setting up proper observing *and* you're observing the right thing, then yes, it should work. Of course nobody can tell you what's wrong if you don't **post your code**.
----
I just figured it out!  Thank you all for your help.  I did have a use for your -fetch: suggestion above in the end, thank you!  I just posted the solution above to help others who may want to do this.

