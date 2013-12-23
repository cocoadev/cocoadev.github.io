---
layout: page
title: OverComplexGuiSimplification
---

I've been working on a ludicrously complicated simulation program for robotics for over a year now, and since it's blossomed in many ways from when I began it, it has somewhat accordingly bloated in terms of its GUI.

Now, I don't mean to toot my own horn, but the codebase itself is pretty damn tight. I do full scale reviews every month or so to see what can be streamlined, what can be turned into a generalized mechanism, and so on and so forth. It's actually very maintainable. HOWEVER, the GUI is pretty absurdly voluminous.

Here's a screenshot of the program running with every inspector panel open -- the only thing not visible is the Console, which is the other tab on the drawer hanging off the bottom of the main window.  I should point out that the panels all represent important functions, so I can't just get rid of them.

I made the screenshot a PNG so this wikki wouldn't inline it, since it's so big it would break up page layout:
http://home.earthlink.net/~zakariya/files/TooComplex.png

Suffice it to say, it's a fair bit too big to fit with all windows open on my development machine's screen ( A 12" PB ) which is ironic, in a way.

What I'm wondering is, is if anybody has any ideas to clean this up, in a HIG sort of way. My thought is to have all the panels share one panel and expand and contract like in MS Word X, Flash MX & Omnigraffle:
http://www.omnigroup.com/images/newimagestemp/omnigraffle/licky.jpg

Any ideas? I'd appreciate it something fierce.

-- ShamylZakariya

----

I don't think you need to go the OmniGraffle way of doing things - at least not yet. there are a few large panels, and I think that technique works better when you have a dozen or so small panels. The Force panel can be shrunk down immensely by rearranging the buttons and text fields and using the small control size.

Could the Variables panel be a tab in the Log/Console section?

Also, do you need the codec/quality section of the Movie Control panel? You can probably put this in a sheet after hitting "save" or something similar. I don't know how your application works, but it might be a good idea to make play/record/stop buttons (which wouldn't take as much room) for this panel.

Another thing, the split view may not serve the best purpose for the ROM Tree panel because the lower half doesn't resize vertically (or it doesn't appear to). This results in waisted space at the bottom. If you want the ability to hide the lower half of the view, a disclosure triangle might work better.

Designing the user interface is all about deciding what the user needs access to quickly. If they don't need to access it frequently, consider taking it off the panel and making it a menu item or something similar.

-- RyanBates

----

I'm not sure I completely understand what I'm looking at here, but you could reduce the panels' size immensely (and possibly improve usability as well) by getting rid of the text fields and popup menus below the table views and just adding editable columns for the various controls. Also, don't forget about sheets - you might want to add a 'More Options...' button where appropriate and open up a sheet on the panels for things that don't need to be immediately visible.

The Movie Control panel could use a disclosure triangle for the codec/quality section, and there's a lot of dead space at the bottom of the Rom Tree panel that could be removed (unless you're using that space for some kind of status display.)

----

On a different page (DrawerAbuse) you stated that you made some panels smaller. Would you mind showing us a picture of the smaller panels? Is this the one? [http://www.home.earthlink.net/~zakariya/files/ComplexPanel.png] If so, I have some suggestions. -- RyanBates

----
That was a pic of the old ROM Tree Panel ( ROM stands for Robot Object Model, and it's the way you can "inspect/control" a servo, a motor, a sensor, or compass or what-have-you ). The trouble there was that in my API, you "deposit" goals for the runtime via the root node of the ROM Tree. The root node is a "special" node and can be thought of as the primary gateway between my AI logic and the hardware. From an API standpoint, it makes sense, but I was being a little too literal I think making the root node of the ROM Tree show you the goal inputs. I've since moved them out of the tree into a separate view and have made the tree rootless.

Here's a new screenshot :
[ http://www.home.earthlink.net/~zakariya/files/TooComplex2.png ]

Mind you, I don't approve of the two drawers. This is temporary, I was just curious wether I liked having the behavioral inputs on the main window in a ddrawer, and I think I do.

Regarding the other panels:
Now, the ROM Tree panel has a fixed 90 pixel tal bottom half to contain the GUI for the selected node. This works great, and allows for a smaller panel overall. It used to be large and have a splitter because the Root element GUI tended to be large and complex.

The Force panel is much more compact.

The Movie panel is the same. By and large, the movie panel isn't used too often; only when you want to record something. So I decided to leave it as is.

I'm probably deprecating the Variables panel, since I haven't actually *used* that API feature ( part of my core runtime ) since september of 2003.

The bottom drawer contains the Log and the Console. The console is just like stdout, but in a text view -- it's useful for runtime tracing. No biggy. The log gives nice time-stamped and categorized messages and is useful for formalized event logging. I don't know if I should put those two in a separate panel or what.

Anyway, and suggestions or ( hint hint ) potential mockups would be appreciated ;)

--ShamylZakariya

----

Honestly, I don't think it's that bad. If you don't use the movie panel very often, you can keep it closed and all you have are two panels. I would need to know a little more about your application before offering suggestions on how to make the two panels less complicated. You can still do some rearranging to cut down the size a little more, but it's pretty close.

As for the drawer - if it were me, I would prefer a panel, but the contents are so closely related to the main window that it kind of works as a drawer. I was able to place the contents of that drawer in a 230w x 170h pixel panel and still have it look clean and organized. The numeral values are text fields as well so the user has an alternative to the slider. I would show it to you, but I don't have access to a decent server space. Just for kicks, I decided to make an ASCII version (hope it turns out). ;)

    
+---------------------------------------+
|                                       |
| Rotate:    [0.00]   Sidestep:  [0.00] |
| --------O--------   --------O-------- |
|                                       |
|                                       |
| Stance:    [0.00]   Heading:   [0.00] |
| --------O--------   --------O-------- |
|                                       |
|                                       |
| Speed:     [0.00]   Velocity:  [0.00] |
| --------O--------   --------O-------- |
|                                       |
|             [ ] Wander                |
+---------------------------------------+


If you e-mail me, I can send you the real image. Address is on my page. -- RyanBates

I like the idea, it does seem probably better than my current vertical-list-in-a-scrollview approach. But, since it would be wide it would have to go in a panel, instead of a drawer. Though that's OK.

I'm sorta liking having them in a drawer just because the panels are for inspection of aspects of the runtime, and the behavior inputs modify what's happing in the engine. It seems logical to me, though I may just be practicing self-justification. 

--ShamylZakariya

----

One thing I neglected to mention, about the behavior inputs, is that they're specific to the simulation being run. For example, the quadruped robot has the six range inputs above & a boolean. That's what I showedin the screenshots. But I also have a wheeled platform and its inputs are different: since it requires a completely different behavioral architecture to control it.

Because of this, I took the simple route and went for a vertical stack, one element per input. A matrix/grid type layout would make sense but would be slightly harder given the dynamic nature of the inputs.

This of course might make for an interesting experiment. I've written layout managers before, ( for BeOS ) and for my vertical stacking I wrote a "sort" of layout-managing stacker subclass of NSView. I might want to write some kind of general purpose layout manager to arrange the input views optimally for panel height/width.

Does anybody know if there are grid-type layout managers for Cocoa? Or am I going to have to roll it myself, like I did for BeOS ( which has even worse support for automatic layout than Cocoa ).

--ShamylZakariya

----

I've just been checking out MOKit, and it has a MOViewListView class which might be helpful to you (or it might not). It's basically the same as your original OmniGraffle idea.

----

I've spent some time refactoring, though admittedly not enough, as I've been simultaneously exploring some very interesting learning mechanisms. Anyway, I wanted to post a new screenshot and see what you all think. All I've done is pull off one of the drawers and improve the side-drawer ( "PANSI Runtime Input" ). 

http://home.earthlink.net/~zakariya/files/TooComplex3.png

Also, a neat addition I made is a tracing mechanism which you can see here:
http://home.earthlink.net/~zakariya/files/Trace.png

The trace flows the views in it in a grid, and automatically handles scrollbars. I submitted my layout code as FlowLayoutView. It's really keen. I also use it to layout the PANSI inputs, albeit in single-column mode.

But this brings up a question: Perhaps this whole monstrosity warrants a one-window solution? I recently tried out Eclipse, mainly just to see what it offered, but I was *very* impressed at the multi-pane one-window GUI. Now, I admit, Eclipse is designed to be an IDE. But then, this program I'm developing is also a developer tool.

What I mean is, if you look at the screenshot you can see how I've arranged everything into a crude rectangle -- to fit my screen. Well, why not just make a big window with dynamic layout to position the panels as views within it.

Would this go WAY in the face of Mac style-guidelines?

--ShamylZakariya

*No more than ProjectBuilder did. Personally, I find the ability to move windows around wherever is convenient to be quite useful. You might want to implement MagneticWindows as a compromise solution.*

