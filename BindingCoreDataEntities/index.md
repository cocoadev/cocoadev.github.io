---
layout: page
title: BindingCoreDataEntities
---



Hello! I'm develping a school gradebook app, and I'm having trouble getting CoreData to work. I have two entities, Student and Activity, and array controllers in IB. Both entities have attributes and relationships to each other. In IB I have a table with two columns: student and grade. The table's connected to the student array controller. Another table holds a list of activities, connected to the activities array controller. What I want to do is have the grade column show the respective students' grade for the selected activity, and change when selection of activity changes. I've tried everything I can think of. Is there an easy way to do this? Thanks, --LoganCollins

----
This seems like a popular homebrew application. I am writing one for my father's use at the moment (4 June 2006). From my experience it is an excellent way to get a thorough understanding of CoreData, InterfaceBuilder, and CocoaBindings.

Here are the aspects of the development that I will cover:

*Maintaining common Activities with separate grading per Student
*Populating tables using InterfaceBuilder (IB) and CocoaBindings


**Maintaining common Activities with separate grading per Student**

First, the Data Model needs to be changed. I suggest adding a bridging object, Report, between Students and Activities to your Data Model. Here are the Data Model changes I suggest to achieve that:

(Entity)Student:

*[...whatever attributes and relationships you need otherwise]
*relationship(Report 0..*)**reports**(inverse **student**)


(Entity)Activity:

*[...whatever attributes and relationships you need otherwise]
*attribute(String)*name**
*attribute(Decimal)**weight**
*relationship(Report 0..*)**reports**(inverse **activity**)


(Entity)Report:

*[...whatever attributes and relationships you need otherwise]
*attribute(Decimal)**mark**
*relationship(Student)**student**(inverse **reports**)
*relationship(Activity)**activity**activity**(inverse **reports**)


I use the following algorithm to programmatically generate Reports for Students across a set of common Activities (I am using your terminology rather than mine for clarity):

Activity custom NSManagedObject subclass:
    
**function** awakeOnInsert
    **for each** student **in fetch of all** Students
        **create** new Report
        **relate** the new Report's activity to this Actity
        **relate** the new Report's student to the student of this iteration
        **add** the new Report to student of this iteration's list of reports


Student custom NSManagedObject subclass:
    
**function** awakeOnInsert
    **for each** activity **in fetch of all** Activities
        **create** new Report
        **relate** the new Report's student to this Student
        **relate** the new Report's activity to the activity of this iteration
        **add** the new Report to this Student's list of reports


**Populating tables using InterfaceBuilder (IB) and CocoaBindings**

To get the Reports to show up in InterfaceBuilder you will need a new ArrayController for Report entities (that has **managedObjectContext** bound to File's Owner's **managedObjectContext** key) and whose **contentSet** is bound to the Student ArrayController's controller key **selection** and model key **reports**.

(NSArrayController)Students:

*Entity: (Student)
*managedObjectContext: **File's Owner** model key **managedObjectContext**


(NSArrayController)R<nowiki/>eportsForSelectedStudent:

*managedObjectContext: **File's Owner** model key **managedObjectContext**
*contentSet: **Students** controller key **selection** model key **reports**


With these NSArrayController's you can now bind a table called Reports in your Student record view with columns Activity, Mark, and Weight as follows:

Reports table in Student view:

*Activity column **value** binding: **R<nowiki/>eportsForSelectedStudent** controller key **arrangedObjects** model key **reports.name**
*Mark column **value** binding: **R<nowiki/>eportsForSelectedStudent** controller key **arrangedObjects** model key **mark**
*Weight column **value** binding: **R<nowiki/>eportsForSelectedStudent** controller key **arrangedObjects** model key **weight**


I hope this helps. Please contact me if you are having trouble. My contact info is on my profile page linked below.

--AlainODea (4 June 2006)
*

