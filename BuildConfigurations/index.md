---
layout: page
title: BuildConfigurations
---



New concept introduced with Xcode 2.1
(or more correctly, new design for an old concept).

This solves this kind of issues, due to inconsistent/ambiguous/confusing user interface:

see DeploymentBuildWithNewXCode

Here is how it seems to work:


*
BuildConfigurations are templates that each target can start with, and then each target can modify the settings.

*
The names used for the different BuildConfigurations are just... names. The template build settings can still be modified in different ways by the different targets

*
Different builds done on the same target with different BuildConfigurations are stored separately, so you can switch Deployment/Development without having to rebuild from scratch. This is really neat (and at last possible!!).

*
Dependencies of a particular target are built with the same BuildConfigurations as the parent (the build settings could still be different, though, because just the name of the configuration is the same, and the initial values for the build settings, but these can still be configured for each target).

*
You can select multiple targets or multiple BuildConfigurations (actually, all of them or each individually) and it behaves as expected to modify several things at once.

*
It breaks compatibility with Xcode 2.0 (ironically, 2.0 did not break 1.5... what's in a number), and 2.1 introduces a new extension 'xcodeproj'. Too bad they had to do that, it almost makes the whole thing ugly!!

*It makes more sense then ".xcode", which gives no impression of it being a document. ".xcodeproject" makes more sense to me, though. The new extension probably corresponds to the new file format. --JediKnil (still stuck with Panther)*

This is covered in the release notes. The new format is more SCM friendly. Xcode offers to make a copy in the new format and open that, which is an improvement over earlier format changes, which would irreversably convert the project file.

In the release notes, they say they had to change the file format to accept the new BuildConfigurations design, but yes, you are right, I heard they also made the format SCM-friendly. A diff used to show everything as changed, now it is much smarter and should even allow merge (or is that too crazy?).

*I have successfully merged project files after another programmer and I modified it simultaneously while checked out from subversion. I was not optimistic but it worked fine. No doubt it will depend on what exactly was changed, but in this particular case, the new file format was wonderful.*

*
There is also the possibility to add your own templates for BuildConfigurations using files. The format is very simple and SCM-friendly. It is not xml, it is just one line per build settings, each line being e.g. SDKROOT = "path/to/the/sdk". * It should be an OpenStep PropertyList file if I'm not mistaken. *

