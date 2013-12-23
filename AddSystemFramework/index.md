---
layout: page
title: AddSystemFramework
---

The following is the code mentioned in the example that I talk about in PerlObjCBridge

In Project Builder, create a new 'cocoa framework' named 'AddSystemFramework'.
Add 2 Objective C classes to it: Addclient and Addserver.
Now add the following code to the files:


*
Addclient.h
    
#import <Foundation/Foundation.h>
@interface Addclient : NSObject
- (int)firstNumber;
- (int)secondNumber;
@end


*
Addclient.m
    
#import "Addclient.h"
@implementation Addclient
- (int)firstNumber { return 0; }
- (int)secondNumber { return 0; }
@end


*
Addserver.h
    
#import <Foundation/Foundation.h>

@protocol Addserverclient
- (int)firstNumber;
- (int)secondNumber;
@end

@interface Addserver : NSObject
- (int)addNumbersForClient:(id <Addserverclient>)client;
@end


*
Addserver.m
    
#import "AddServer.h"
@implementation Addserver
- (int)addNumbersForClient:(id <Addserverclient>)client { return 0; }
@end




----
Here is the code for addSystemFramework.pm:
    
#!/usr/bin/perl

use Foundation;

package AddSystemFramework;

@ISA = qw(Exporter DynaLoader PerlObjCBridge);
@EXPORT = qw( );


$path='AddSystemFramework.framework';

$bundle=NSBundle->bundleWithPath_($path);
die "could not find framework at $path" unless ($bundle and $$bundle);

$bundle->load() or die "could not load framework";

my $identifier=$bundle->bundleIdentifier();
if ($identifier and $$identifier) {
	$identifier=$identifier->cString();
}
else {
	$identifier="unidentified framework";
}
print "framework '".$identifier."' loaded...\n";


1;


----
Here is the code for addserver.pm:
    
#!/usr/bin/perl

use AddSystemFramework;
use Addclient;

package Addserver;
@ISA = qw(PerlObjCBridge);
@EXPORT = qw( );

sub new
{
   my $class = shift;
   my $self = {};
   bless $self, $class;
   return $self;
}

sub addNumbersForClient_
{
	my($self, $client) = @_;
	print "received request from ".$client->description()->cString()."\n";
	my $first = $client->firstNumber();
	my $second = $client->secondNumber();
	print "adding $first and $second...\n";
	return int($first + $second);
}

PerlObjCBridge::preloadSelectors('Addserver');

1;


----
Here is the code for addclient.pm:
    
#!/usr/bin/perl

use AddSystemFramework;

package Addclient;
@ISA = qw(PerlObjCBridge);
@EXPORT = qw( );

sub new
{
	my $class = shift;
	my $self = {};
	bless $self, $class;
	$self{'firstNumber'} = shift;
	$self{'secondNumber'} = shift;
	return $self;
}

sub firstNumber
{
	my($self) = @_;
	return $self{'firstNumber'};
}

sub secondNumber
{
	my($self) = @_;
	return $self{'secondNumber'};
}

PerlObjCBridge::preloadSelectors('Addclient');

1;



----
Here is the code for runserver.pl:
    
#!/usr/bin/perl

use Addserver;

#PerlObjCBridge::preloadSelectors('AddServer');

$server = new Addserver;
$connection = NSConnection->defaultConnection();
$connection->setRootObject_($server);
$connection->registerName_(NSString->stringWithCString_("AddServer"));

print "the server is running...\n";
print "waiting for requests...\n";
NSRunLoop->currentRunLoop()->run();


----
Here is the code for runclient.pl:
    
#!/usr/bin/perl

use Addclient;
use Addserver;

die "usage: perlClient <firstNumber> <secondNumber>\n" unless @ARGV == 2;

# create client
$client = new Addclient (@ARGV);

# create connection to server
$name = NSString->stringWithCString_("AddServer");
$server = NSConnection->rootProxyForConnectionWithRegisteredName_host_($name, 0);
if (!$server or !$$server) {
	print "Can't get server\n";
	exit(1);
}
$server->retain();

printf "response from the server: %d\n", $server->addNumbersForClient_($client);


----
CharlesParnot

