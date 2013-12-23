---
layout: page
title: PBNewFilesCompanyName
---

Does anybody know how to set __MyCompanyName__ in newly created project builder files to something meaningful?  Can I specify an var somewhere to have that automatically replaced with something else?

Type     defaults write com.apple.ProjectBuilder 'PBXCustomTemplateMacroDefinitions' "{ORGANIZATIONNAME = 'your Company Name'; }" into the terminal (replacing yourCompanyName with your company name of course).  -- Bo

FYI - the above works fine for Project Builder, but does not work with XCode. It turns out that you can get it to work, however, with a different line:
     defaults write com.apple.Xcode PBXCustomTemplateMacroDefinitions '{ "ORGANIZATIONNAME" = "Your Company Name" ; }'

That's interesting.  Thanks!  Is there a way to have ORGANIZATIONNAME be set in the project file, somewhere in the target?

----

You could also just change the template for the files:

To replace the __MyCompany__ in main.h of every new Cocoa Application you create simply go to:

/Developer/ProjectBuilder Extras/Project Templates/Application/Cocoa Application/main.m

and change this block:

    
//
//  main.m
//  �PROJECTNAME�
//
//  Created by �FULLUSERNAME� on �DATE�.
//  Copyright (c) �YEAR� �ORGANIZATIONNAME�. All rights reserved.
//

to something like this:

//
//     File: main.m
//  Project: �PROJECTNAME�
//
//  Created by �FULLUSERNAME� on �DATE�.
//  Copyright � �YEAR� My Software Company. All rights reserved.
//


Now everytime you create a new Cocoa Application, the company will be set for you. Of course you would have to change this for all the templates, you plan on using.

-- JacobHazelgrove

----

That works too.  You can also put templates in the same path inside your home directory and they'll override the ones in the main location.  For example, if instead of changing the original main.m above, you copy the Cocoa Application folder to ~/Developer/ProjectBuilder Extras/Project Templates/Application/ (which you'll probably have to create) and then make any changes to the copied template files, then ProjectBuilder will use those instead of the originals.  This also prevents your changes from being overwritten during future upgrades (though with XCode on the way, they're probably gonna break anyway).  -- Bo

*XCode puts them in ~/Library/Application Support/Apple/Developer Tools/*

PS  BTW, if anybody does know a way to set these macros on a per-project or per-target level, then I'd be interested too.

**Per-target isn't very likely IMHO because files in one project can be stuck in various targets at the same time. But per-project would be quite nice.**

