---
layout: page
title: CompareTwoFolders
---

see also WhenADirectoryIsAPackage

I am chasing a bug (reported at Mac Fix It) in which the OS is not reporting certain files as not being copied, when copying a folder over a network. Since no errors are reported, you have to look in every folder, subfolder, and even package contents to see if there are any files missing.

I want to build a cocoa app that compares the contents of a Source and Target folder and reports any missing files. If there are any, there should be a button to cause the missing files top be copied over from the source to the target.

I have done a little layout in Interface Builder, but don't know what steps to take to build in the code.

----

Programming can be viewed as a process of breaking down a task into smaller pieces. You might break it into pieces such as getting folders from the user, getting the full contents of a folder, doing the comparison, finding the missing files, copying them over. You then break it down further, where "getting the full contents of a folder" might become "get the top-level contents of a folder, get all subfolders of a folder, construct a complete list using these capabilities", and so forth. You to break your problem down into subtasks, and work on them one by one. Don't know how to get a folder from the user? Start reading documentation and guides that talk about that sort of thing. If you get stuck on some specific part of this whole thing, feel free to make a page here or ask for help on another resource. But as it is, the question you've asked is simply too vague and general; it's like going into a construction workers' break room and asking, "how can I build a house?"

----

You need an input from the user that specifies the paths to the source and the target result after the network copy. You already have a neat problem of just getting the contents of one (or perhaps both?) of these over the network. This in itself is not trivial. If it was, then I would already be able to explain to you how to do this. There are pages and pages here about using Cocoa to do things over a network when security issues are present. I've just started to understand that the URL is perhaps the recommended way of getting Cocoa to deal with file locations hither and yon.

The Unix-like operating system behind OS X certainly has mastered the business of listing recursively the contents of a directory and all its contained subdirectories. This is done on the command line with something like     ls -laR. The first step in your journey might be simply to find a way to report the output from this command in the content area of an NSTextView. See BCWrapperSource for an example of how this is done in a general way.

----

META: But, as someone famously said, "If you want to bake a cake you must first invent the entire universe." I don't know how, exactly, the notion has arisen that one can simply imagine an app, speak the words that describe it, and POOF! some relentlessly-cheerful IDE marketed by the Sirius Cybernetics Corporation will just about code the thing FOR you. Maybe it's RealBasic that's done it. Maybe it's the fact that the Sirius Cybernetics Corporation gives away its DeveloperTools for free with every shrink-wrapped copy of its relentlessly-cheerful operating system: "HI! I'm Eddie, your shipboard computer, and I'm just pleased as PUNCH to sort out your folder and file difficulties for you today....".

----

META: this phenomenon occurs from time to time and probably has nice WordsJammedTogether we can use. Something like VagueQuestions, BreakItDown, etc. This could probably get its own page with explanations, which could then be linked to for the occasions when a really general question like this gets asked. Any comments or suggestions on that? -- PrimeOperator

----

Nice idea, Prime, but you might have seen from the later questions, "why is it called a "nib" file? what do the letters NS mean?" that the opener was a GettingStarted kind of question that attempts to evade the confession, and we already have that department covered. VagueQuestions could be a valuable adjunct to that... CocoaDev provides a valuable service for the NewbieQuestion such that the newbie can verbalize his or her confusion, get a more-or-less soothing :-) response from a vibrant online community, and then resolutely get back to the good task of LearningCocoa. I think it's important to recognize that CocoaDev performs this service, usually in MailingListMode, but occasionally as TheoreticalCocoaDiscussions, in addition to  its normal informational functions. -- BoozeDog (who always signs his rants now)

