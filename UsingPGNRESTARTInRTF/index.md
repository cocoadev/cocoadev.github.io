---
layout: page
title: UsingPGNRESTARTInRTF
---



Hey guys.  I'm writing a screenwriting program right now that exports RTF files.  I'm trying to get everything in standard screenplay format, and have been mostly successful thusfar.  The biggest problem I have been having is with page numbering.  I can't seem to find the proper syntax for \pgnrestart.  I want the titlepage to be unnumbered and then the numbers to start from 1 on the next page (which is technically the second page of the document).  Unfortunately, RTF does not seem to be well documented.  All I have found are specifications and a couple of primers, but nothing that has helped me solve this problem.

The code below works great, except the numbering in \sect starts on page 2.

Thanks much.  Hopefully, someone is better at this than I seem to be. =D
    
{\rtf1\mac\deff1
{\fonttbl
{\f0\fnil\fcharset77 Gill Sans;}
{\f1\fswiss\fcharset77 American Typewriter;}
}
{\colortbl;\red255\green255\blue255;\red0\green0\blue0;}
{\info {\title $SCRIPT} {\author $REVISER} {\company Slip A Buck}}
\margl1440\margr1440\margb1800\margt1800\pgnstart1
\pard\tx560\tx1120\tx1680\tx2240\tx2800\tx3360\tx3920\tx4480\tx5040\tx5600\tx6160\tx6720\pardirnatural
{\titlepg\sa240\qc
\
\
\
\
\
\
\
\
\
 {\f1 $SCRIPT}\
by\
$AUTHOR\
$CONTRIBUTORS\

{\footer\f0\qj\i This script was generated using the Janeti collaborative storyboarding system and should only be used in productions for the Slip A Buck.com community.  Thanks for supporting the indie scene! =)}
}
{\sect\sectd\pgnrestart\pgnstarts1
{\header\pard\qr\plain\f0\chpgn\par}
{\footer\f0\qj\i This script was generated using the Janeti collaborative storyboarding system and should only be used in productions for the Slip A Buck.com community.  Thanks for supporting the indie scene! =)}

{\pard\f0\fs24\expnd0\expndtw0\kerning0\li360\caps $LOCALE\caps0\par}
\
{\pard\f0\fs24\expnd0\expndtw0\kerning0\li360 $TAG \par}
\
{\pard\f0\li3340\caps CHARACTER\caps0\par}
{\pard\f0\li1800 Dialogue.\par}
}
}



----
