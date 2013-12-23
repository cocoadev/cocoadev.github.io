---
layout: page
title: ExportTypeIdentifiers
---

Here is a script that parses an Info.plist file and outputs the XML code needed to export those UTIs that the system does not know about. It uses the ListAllDeclaredTypeIdentifiers program to find out which UTIs are already declared on your system. It is a bit hackish, but it can save you a lot of work if you declare a whole lot of new types. It relies on the Info.plist being indented with tabs in the same way that Xcode does! 

It takes the path to an Info.plist file as an argument, and optionally you can list any UTIs you want to conform to.

-- WAHa

    
#!/usr/bin/perl

use strict;

my $lstypes_command="./lstypes";
my @definedtypes=grep $_,split /\n/,`$lstypes_command`;

die "Usage: exporttypes.pl Info.plist [types-to-conform-to ...]" unless @ARGV>=1;

my $plist;
open PLIST,$ARGV[0] or die "Couldn't open file \"$ARGV[0]\"";
$plist.=$_ while(<PLIST>);

my $conform;
$conform="\t\t\t<array>\n".(join "",map "\t\t\t\t<string>$_</string>\n",@ARGV[1..$#ARGV])."\t\t\t</array>"
	if(@ARGV>1);

my $declaredtypes;
$plist=~m!<key>UTExportedTypeDeclarations</key>\s*^(\t*)<array>\s*^(.*?)^\1</array>!sm;
$declaredtypes.=$2;
$plist=~m!<key>UTImportedTypeDeclarations</key>\s*^(\t*)<array>\s*^(.*?)^\1</array>!sm;
$declaredtypes.=$2;

@definedtypes=grep $_ ne $1,@definedtypes
	while($declaredtypes=~m!<key>UTTypeIdentifier</key>\s*<string>(.*?)</string>!msg);

$plist=~m!<key>CFBundleDocumentTypes</key>\s*^(\t*)<array>\s*^(.*?)^\1</array>!sm;
my $types=$2;

my %types;
while($types=~m!^(\t*)<dict>\s*^(.*?)^\1</dict>!msg)
{
	my %type;
	my $typedef=$2;
	$type{$1}=$2 while($typedef=~m!<key>(.*?)</key>\s*^(\s*<(\w+)>.*?</\3>|\s*<\w+/>)!msg);

	my ($id)=$type{LSItemContentTypes}=~m!<string>(.*?)</string>!;

	$types{$id}=\%type if $id and !grep $id eq $_,@definedtypes;
}

print "\t<array>\n";

for(sort keys %types)
{
	my $type=$types{$_};
	print "\t\t<dict>\n";

	print "\t\t\t<key>UTTypeIdentifier</key>\n";
	print "\t\t\t<string>$_</string>\n";

	if($$type{CFBundleTypeName})
	{
		print "\t\t\t<key>UTTypeDescription</key>\n";
		print "$$type{CFBundleTypeName}\n";
	}

	if($conform)
	{
		print "\t\t\t<key>UTTypeConformsTo</key>\n";
		print "$conform\n";
	}

	if($$type{CFBundleTypeIconFile})
	{
		print "\t\t\t<key>UTTypeIconFile</key>\n";
		print "$$type{CFBundleTypeIconFile}\n";
	}

	print "\t\t\t<key>UTTypeTagSpecification</key>\n";
	print "\t\t\t<dict>\n";

	if($$type{CFBundleTypeExtensions})
	{
        	print "\t\t\t\t<key>public.filename-extension</key>\n";
		$$type{CFBundleTypeExtensions}=~s/^\t\t\t/\t\t\t\t/gm;
		print "$$type{CFBundleTypeExtensions}\n";
	}

	if($$type{CFBundleTypeMIMETypes})
	{
        	print "\t\t\t\t<key>public.mime-type</key>\n";
		$$type{CFBundleTypeMIMETypes}=~s/^\t\t\t/\t\t\t\t/gm;
		print "$$type{CFBundleTypeMIMETypes}\n";
	}

	if($$type{CFBundleTypeOSTypes})
	{
        	print "\t\t\t\t<key>com.apple.ostype</key>\n";
		$$type{CFBundleTypeOSTypes}=~s/^\t\t\t/\t\t\t\t/gm;
		print "$$type{CFBundleTypeOSTypes}\n";
	}

	print "\t\t\t</dict>\n";
	print "\t\t</dict>\n";
}
print "\t</array>\n";

