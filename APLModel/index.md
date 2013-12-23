---
layout: page
title: APLModel
---

A closed XML-based file format used by Apple for defining layout, options, and behavior for printing. So far, it's only been used in iCal, where it can be seen in dayview.aplmodel, fullview.aplmodel, and weekview.aplmodel. The format appears to consist of a bundle containing standard info.plist and version.plist files. In the resources directory of the bundle is the model.xml file, the most important part of the format and the file that actually defines the printing behavior. Associated .lproj directories also live here for localized strings.

This format seems to be related to another mysterious format that's also only used in iCal: calcols, which appears to define gradient and shading behaviors.

----

Discussion:

While browsing around in iCal's resources directory, I discovered several interesting files ending in an extension I had never seen before: .aplmodel. fullview.aplmodel and dayview.aplmodel, for example. Take a look at these files. They contain what seem to be XML definitions of UI. An excerpt:

    
 <page-templates>
   <template name="root-template" version="0.1" entity-types="detail, detail/2">
     <elements>
       <group name="root-group" packer="root-packer">
         <group name="header-group" packer="header-packer" vertical-resizebehavior="can-grow:no">
           <group name="calendar-group" packer="calendar-packer" cangrow-vertically="yes">
             <element class="APLPHeaderElement" alignment="leftv" big-string="%{list-string}" small-string="%{date-string}"/>
             <element name="calendar-keys" class="APLPCalendarsElement"/>
           </group>
           <group name="minimonth-group" packer="minimonth-packer" vertical-resizebehavior="can-grow:no" horizontal-resizebehavior="can-grow:no">
             <element name="minimonth" class="APLPMiniMonthElement" variant="list"/>
             <element name="minimonth2" class="APLPMiniMonthElement" variant="nextlist"/>
           </group>
         </group>
         
         <group class="APLPBorderedGroupElement" name="details-group" packer="content-packer" top="1">
           <list name="details-list" class="APLPBorderedGroupElement" right="1" element-class="APLPEntityDetailsElement" entity-type="detail" show-phonenumbers="yes" packer="details-packer"/>
           <list name="details-list" element-class="APLPEntityDetailsElement" entity-type="detail/2" packer="details-packer" show-phonenumbers="yes" always-visible="yes"/>
         </group>
         
         <group class="APLPBorderedGroupElement" top="1" packer="footer-packer" vertical-resizebehavior="can-grow:no">
           <element class="APLPFooterElement" name="footer" variant="week" left-string="%{timezone-string}" right-string="%page%"/>
         </group>
       </group>
     </elements>


It goes on like this for some time. A quick Google search yields not the slightest mention of the format, and given that there's no DTD specified, it's something of an enigma. There's an additional mysterious file with the extension .calcols, which seems to be linked into the aplmodel for... gradient definition? That's what it looks like. This raises the question: is Apple beating Microsoft to the punch on XML-based interfaces? Have they already and just not mentioned it? Is a tool for creating these kinds of custom views already available in-house? Will it be included in Xcode 3? Your thoughts, ladies and gentlemen? -- AndyMatuschak?

*
I dunno, but I protest the use of the term "root-packer" ... it sounds like an epithet ... ;-) Good catch, though, I have REALLY been wanting to see advancements in Interface Builder; it seems like the neglected tool and, IMHO, could stand an overhaul.*

It's a likely assumption. It almost seems like iCal is Apple's testing ground for new ideas - I've spent the last few months digging through iCal (writing an InputManager to make the damn thing work like a decent calendar), and many classes in CALCore look like precursors to what you find in the Cocoa framework. (It's really obvious if you look at their preference pane handling.) Or maybe iCal is just old, and nobody at Apple cares about it any more... -- RobertBlum

