---
layout: page
title: WorkingWithFrameworksDiscussion
---


A comment on the General/SparkleUpdater page spawned this topic for me -> "Frameworks are unwieldy"

I've written a few Frameworks, and have used several. Frameworks have never seemed unwieldy. Integrating them with my apps always seemed straightforward. My experience with producing them for others was similar. I've never received any feedback (positive or negative) regarding how others have perceived integrating my frameworks with their code.

I'll concede that I've only used frameworks on a handful of projects. The techniques I used to integrate them were similar. On the framework that I support for others, I followed conventional style, so perhaps I'm lacking a breadth of experience.

Please - comment on your use of various frameworks. Things that worked like magic. Things that sucked. Things that you find to be just a little awkward. Severe limitations. Wonderful freedoms.

----

I put all code which could be shared between projects (custom controls, useful categories etc) into a framework which I include into each of my projects as an external target to save on duplicate code. It works really nicely and General/XCode handles it well.

----

Will Shipley recently wrote an essay on his blog which detailed a few very good reasons why he dislikes frameworks; whether you're for or against them, it's worth reading.

http://wilshipley.com/blog/2005/11/frameworks-are-teh-suck-err.html

For my own projects, I have a library folder which contains all my shared source. When I'm working on a project, I just import the actual source file into xcode, except I link it from it's own directory, instead of copying it. Compared to using a framework (or anything else really), it's VERY easy. The only downside I can see (which I think is mentioned in Shipley's essay) is when you make a change in a shared file that could unknowingly affect another project. I haven't run into this yet, but as long as you're using good source control practices, I don't think there's too much danger.

----

My app has about 14 plugins it uses, so I have a huge need for a framework. It works very nicely, since I just jam all my reusable code into the framework and call it from each plugin.

----

OP here. I read Wil Shipley's article mentioned above. His points were fairly well argued, and he was careful to qualify his statements. I kept saying to myself "well of COURSE you wouldn't want to use frameworks in that scenario". He then fails to give a balanced article by fully outlining the situations that frameworks were designed to handle. Developer consumed software is the niche I fall in 95% of my working life. I had to keep reminding myself that I wasn't his audience.

I'm currently writing a software suite that I hope will one day mature from a hobby to a nice little endeavor. The principal piece of this software suite is the framework. I also have a GUI app to aid in object graph configuration for the developer.

Instead of having multiple projects, though, I have one project. I have 5 *targets*. One target is the framework. Another is the unit test bundle for the framework. Then I have the target for the GUI app, and a target for the acceptance tests for the GUI app. The fifth target is a loadable bundle that is used for some of my unit tests.

Setting up things this way, as opposed to the 'four separate projects' style, as suggested in Shipley's blog, seems much more natural for General/XCode. One mast build setting can be used as a basis for every target. If you need to override a build setting for one particular target only, you can through the target's specific settings. This doesn't completely solve build setting issues. Keeping track of whether a target takes it's build setting from the target's build setting, vs the project's build setting, is still somewhat problematic. It is significantly easier than having to keep build settings synchronized across several projects.

I guess that regarding Shipley's article, I'd have to conclude that there's another group of developers who benefit from building frameworks. It's rather obvious, but still overlooked in the blog. Those of us who are aiming to DELIVER frameworks to developers.

If I were developing a single consumer app, I can't imagine wanting a framework (plugin architectures being a notable exception). Code I needed to reuse would be checked out of some source code repository. I've been in large development shops. Although none provided consumer apps, I can respect the issues involved in keeping frameworks up to date, versioned, and properly documented.

Still, the point of view I was hoping for - and didn't clarify in my original question - was for framework consumers. For those that use frameworks (besides Foundation & General/AppKit), what are your unique experiences?

----

OP again. Since this thread has gone untouched for so long, I have to conclude that frameworks work just fine for the development community at large. I've certainly never had issues with them. Granted they are not the right solution for every problem, but nothing is.

All in all, the only thing I've learned is that celebrity and success does not equate to skill and wisdom. Wil Shipley's Delicious Monster is a big success, but it doesn't make his 'pearls of wisdom' valid. Tenacity is sufficient for success in any area, regardless of ability.

Not that any of that is related to working with frameworks....
