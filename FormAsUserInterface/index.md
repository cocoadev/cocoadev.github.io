---
layout: page
title: FormAsUserInterface
---

Describe FormAsUserInterface here.

Goal

 

Creating program for office administration use, I would like to use an employee information (paper) fill in the blank form consisting of six (6) pages, text fields, numerical fields and check boxes as the user interface so that the office administrator will be familiar with the layout and information required, allowing the office administrator to fill in each field, the same way office administrator would if they were filling out the paper form and be able to scroll down to the next page as needed (6 pages). The information placed in the Text/Numerical and check box fields will be stored in the employee file similar to an address book with more information in it. 

1)      When employee information window is open the window should appear just like the paper employee form

2)      Allow the office administrator to enter the data in the fields and mark the appropriate check boxes

3)      When complete save to the specific information to the correct and unique employee file.

4)      When administrate wish to view or edit employee information the same window appearing just like the paper form will open allowing the office administrator to edit data and save to employee file.  

To accomplish this would I scan the form?

 What would I save it as .jpg?

 After form is scanned how would I place it in the employee information window?

How would I add the text fields, numerical fields and check boxes to the form?

How would I accomplish this? 

Your help is greatly appreciated.

bocephus_h@yahoo.com
----
The question is very general and indicates a failure on the part of the original poster to perform even the tiniest amount of research into the subject.  There are an infinite number of solutions the the problem, and most don't require any programming at all.  Microsoft Word can be used to create such forms.  Adobe Acrobat Pro can create such forms.  Bento by Filemaker can probably make such forms and includes a database solution as well.  The whole interface could be built with web standards.  Web Objects is a possibility but is likely overkill.

Dealing with just the interface requested, one approach would be to start from the Sketch example program, enhance it to support multiple pages, and set the background image of each page to the appropriate scanned image.  There are separate examples to use as a guide for multi-page applications (such as TextEdit.app) and Sketch already supports image graphics.  Then place text fields and check boxes on the pages either programatically or via Interface Builder.

Another approach would be to use Applescript or F-Script or TCL/Tk or Python. 

The real question that hasn't been asked is how will all of the information entered on the forms be stored and retrieved.  Core Data might be a good choice, but that is an advanced topic, and I think the original poster is too much of a novice to accomplish much of anything (based solely on the question asked).

I keep coming back to Bento.  I have never used it, but is sounds like an ideal solution.

Finally, this poster seems dangerously close to being a Help Vampire http://www.slash7.com/pages/vampires.
HowToAskQuestions
AdvisingCocoaNewbies
CocoaPrerequisites
----
DeleteMe Original poster seems to have gone and/or given up.

