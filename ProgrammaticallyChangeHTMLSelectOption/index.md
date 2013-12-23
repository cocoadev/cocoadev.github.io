---
layout: page
title: ProgrammaticallyChangeHTMLSelectOption
---

ProgrammaticallyChangeHTMLSelectOption


I want to be able to change the page http://www.wrcc.dri.edu/cgi-bin/wea_windrose.pl?caCFEN to different day, month, and year and then click "submit info" programmatically. I've looked at NSURL and don't see any way to do this. Can anybody give me a clue? Here is the html source:

    
<H3> Set the starting date. </H3>
<TABLE> <TR> <TD>
Select the <BR> Month:
</TD>
<select name="smon">
<option value="01">January</option>
<option value="02">February</option>
<option value="03">March</option>
<option value="04">April</option>
<option value="05">May</option>
<option value="06">June</option>
<option value="07">July</option>
<option value="08">August</option>
<option selected="selected" value="09">September</option>
<option value="10">October</option>
<option value="11">November</option>
<option value="12">December</option>


----

Easiest way would be to use curl (see "man curl" in the Terminal, or the CURLHandle framework) to submit the form data via POST.

