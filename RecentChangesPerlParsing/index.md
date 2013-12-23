---
layout: page
title: RecentChangesPerlParsing
---

This is some Perl code I wrote a while ago to parse the RecentChanges page - it doesn't put it into RSS or anything. Probably not that great of parsing but I'm new(b) to Perl. --KevinWojniak

Execute it from Terminal like:
    
curl 'http://www.cocoadev.com/index.pl?RecentChanges' | ./cocoadev.pl


    
#!/usr/bin/perl

while ($line = <STDIN>) {
$html .= $line;
}

if ($html =~ m/<td id="mainContent" colspan="2">(.*)<\/td>/gi) {
$html = $1;
}

$total = 2;
$count = 0;
$lastline = "";

@lines = split(/\n/, $html);
foreach $line (@lines) {

if  ($lastline =~ /^<p>$/i) {
if ($count <= $total) {
print "\n\n" . $line . "\n";
} else {
last;
}

$count++;
}

if ($line =~ /<li>\s*<a href=".*">(.*)<\/a>.* +(.*)/i) {
$page = $1;
$date = $2;

print $page . ": " . $date . "\n";
}

$lastline = $line;
}

Sample output:
    
September 21, 2004
CocoaDevMostWanted: 10:33:54
GeeFive: 09:43:28
SubclassNSTableViewForCellDrag: 09:37:25
WebServicesCore: 09:28:08
QiYang: 01:30:50


----

Ok so I was bored and added RSS to this so it works as a feed for your newsreaders :)

    #!/usr/bin/perl

print "Content-type: text/xml\n\n";

$html = `curl 'http://www.cocoadev.com/index.pl?RecentChanges'`;
if ($html =~ m/<td id="mainContent" colspan="2">(.*)<\/td>/gi)
{
	$html = $1;
}

$total = 20;
$count = 0;
$lastline = "";

print "<rss>\n";
print "<channel>\n";
print "<title>CocoaDev Recent Changes</title>\n";
print "<link>http://www.cocoadev.com/index.pl?RecentChanges</link>\n";

@lines = split(/\n/, $html);
foreach $line (@lines)
{
	if ($line =~ /<li>\s*<a href=".*">(.*)<\/a>.* +(.*)/i)
	{
		if ($count < $total)
		{
			print "<item>\n";
		
			$page = $1;
			$date = $2;
			
			print "<title>" . $page . " (" . $date . ")</title>\n";
			print "<link>http://www.cocoadev.com/index.pl?" . $page . "</link>\n";
	
			print "</item>\n";

			$count++;
		}
	}
	$lastline = $line;
}
 
print "</channel>\n";
print "</rss>\n";

Or if you're lazy you can use what I have on my server: http://www.kainjow.com/scripts/cocoadev.pl - I realize CocoaDevRSSFeed does the same thing and probably better, but his source code isn't public (from what I could tell...) and it isn't instantly updated.

----

*Well, I'm sure if you ask Theo.. :-)*

Not that anyone asked, but here is the PHP code that parses the RecentChanges page for CocoaDevRSSFeed:

    
function CCD2Items( $limit=50 ) {
	// my server runs in safemode, so I don't really use file()
	$lines = file("http://www.cocoadev.com/index.pl?RecentChanges");
	
	$currentDate = NULL;
	
	$items = array();
	
	foreach ( $lines as $line ) {
		if ( preg_match( '|^\w+ \d\d?, \d{4}$|', $line, $matches ) ) {
			// it's a date line, save the date
			$currentDate = date("Y-m-d", strtotime($matches[0]));
		}
	
		if ( preg_match( '/index.pl\?([A-Z]\w+)">\1<\/a>.*(\d\d:\d\d:\d\d)/', $line, $matches) ) {
			// it's an item line, save the item
			$items[] = createItem(
				$matches[1],
				$currentDate,
				$matches[2]
			);
		}
		
		if ( sizeof($items) == $limit ) {
			break;
		}
	}
	
	return $items;
}

function createItem( $name, $date, $time ) {
	$page = implode(*, file("http://www.cocoadev.com/index.pl?$name"));

	return array(
		"title"       => $name,
		"date"        => $date,
		"time"        => $time,
		"description" => $page
	);
}


Naturally, there's a lot more to it, but nothing especially interesting. The items created by     CCD2Items are written to a database, and when a client comes along and want's the feed the database is read and the feed is constructed (in different formats depending on the parameters). Since the data is abstracted and put in the DB the cache-script (above) and the server-script (that outputs the feed) are independent, which also means that the output format is independent of the input format. 

--TheoHultberg/Iconara

