---
layout: page
title: EmulatingFinderFind
---

How would i go about making my app work like the finder find system, with the add criteria buttons and making them add space and control?

----

You'd have to handle it all manually, creating views programmatically and other such fun. -- FinlayDobbie

That said, that's not much harder than making a .nib with your view in it, and then loading repeated copies of the view. -- RobRix

----

any tips on where to start?

----

Well, like I said... make a view in your .nib file and place your search widgets in it. This will be the criteria view. So you'll probably want a little NSTextField and maybe a pop-up menu. Set the file's owner to some class, MySearchController, or something, and wire the "add constraint" button you'll make for adding another criteria to an action on the file's owner, -addConstraint: or something.

Now you just make -addConstraint: load in the .nib again (there might be a better way than this, but this is simple enough), add the view to the window (if you could use a matrix, that would be nice, but matrices work with cells, not views, so I think you're stuck with just views), and change the window's size to fit.

-- RobRix

----

ok, well i think i am getting there. i have made a few custom views, but i cant work out how to make a customview in the window to show more than one view, which was what you were recomending?

i am trying to get it working with the criteria, with the + and - buttons btw, which remove parts of it (which i will make seperate views)

----

I don't quite understand what you are trying to do, but you might look at:

SearchTextFieldCell

That page has links to code that creates a Mail.app like search field.

-- JesseGrosjean

----

OK, here is what i am trying to do,

i am not trying to actually create any kind of serarch, i am just trying to emulate the LOOK of the file find in the finder.

hitting command+f in the finder brings up the find window, and inside the 2nd white box it shows the added criteria. there is an "Add Criteria" drop down menu, which can add bits to the 2nd white box. i am trying to make my app work like this, with things be added to the white box. i want to remove the with the button "-" just like the finder find does.

how can i do this? adding something preset to the window and resizing the window. also, how do i remove it when the user uses the "-" button?

----

**Have the "-" button wired up to a method you'll make like -removeCriteria: on your controller object. The controller can get the "-" button's superview, and remove it from the window. Then it can move any criteria it needs to up, and resize the window to fit. Sound good? -- RobRix**

----

sorry to go on about this but...

1, **how do i make superviews, and display them on demand?** - can you explain this really step by step for me?

2, **how would i find out the the superview of a button, and then with that information remove the superview?**

----

its ok, i got it working with some help from another cocoa programmer. thanks for all the help.

i will send the source to anyone who is interested...

----

I'm more than interested if you could hook me up and lemme see...

----

Yeah, could you post it for download... this is something the entire community could benefit from!

