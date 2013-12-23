---
layout: page
title: BestWayToWriteToBSDDevice
---

Hi,

What is the best solution to write/read to a bsd device (like /dev/cu.modem)?
I tried with NSFileHandle but I am not sure how to handle this with the
at+commands (like: at+cops?)

Thanks for your feedback!

----

This is not a mailing list.  How you do it depends on whether you're attempting to read/write a character or block device.  Since you're talking about AT commands, I will assume character device.  Just read and write like a normal file.

Why you would want to do this is beyond me, but it's your computer, do what you want.

----

Oh I am sorry! I didn't realize that this is not a mailing list! Sorry for that!

But nevertheless - Thanks for your reply!

----

More specifically, give MailingListMode and HowToAskQuestions a read. Both contain important information about how to be a good guest on this wiki. You'll get much better responses by following good etiquette. 

As to *"Why you would want to do this is beyond me, but it's your computer, do what you want."* ... I'm not even sure what that type of response is meant to accomplish.

----

Here's one reason why you might want to do this, which just came up at my place of work last week - controlling a non-standard serial device using some text commands from terminal. Some people actually do use Macs for more than just iPhoto, etc... --GC

----

Should have clarified that I meant "I have no idea why you would want to control a modem," considering OS X does all that for you (and handles winmodems just fine too).

----

Are you a young programmer? If so, I can understand why you'd assume that all people need from modems  are to dial up to an ISP. How about dialing into other types of services? Terminal applications (those most definitely still exist)? How about creating a TAD (telephone answering device) with caller ID and a menu system? Please don't assume just because *you* can't imagine what use someone could have for doing something that there isn't one. If that's the point of your post, please just don't post.
 
----

I was the author of MacDialer, a speed dial application that integrated with your Address Book.  The modem can do much more than just connect to the internet.  Apple provides only so much.  Developers can issue AT command and communicate with the modem to do some interesting things. -G

----
I found myself wondering how to accomplish exactly the same thing a while back, while trying to get my phonebook off my mobile phone. Here's the bit of code I came up with for connecting to the phone; hopefully it's useful to you. For what it's worth, I hereby put this into the public domain.

    
//
// Cocoa Code for Connecting to a BSD Modem and Issuing AT Commands
//
// By Tom McClean, 2007
// tom@pixelballistics.com
//
// This file is public domain. It is provided without any warranty whatsoever,
// and may be modified or used without attribution.
//

#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/ioctl.h>
#include <errno.h>
#include <paths.h>
#include <termios.h>
#include <time.h>
#include <AvailabilityMacros.h>

#import <Cocoa/Cocoa.h>
#include <IOKit/IOKitLib.h>
#include <IOKit/serial/IOSerialKeys.h>
#include <IOKit/IOBSD.h>

@interface Modem:NSObject{
	int file;
	io_object_t modem;
	struct termios settings;
	}
	
	+(NSArray*)scanForModems;
	-(id)initWithModem:(io_object_t)theModem;

	-(int)open;
	-(void)close;
	
	-(BOOL)connect;
	-(void)disconnect;
	
	-(NSString*)name;
	-(NSString*)responseToCommand:(NSString*)command;
		
@end

@implementation Modem

//
//
// Private methods
//
//

	-(BOOL)configureSerialPortWithBaudRate{ 

		//
		//  you'll need to look up the tcsetattr man page to figure out the various settings
		// (i.e. baudRate,wordSize,parity, etc... consider them as pseudovariables here)
		//
 
		BOOL result=YES;
		struct termios newSettings=settings;
		cfmakeraw(&settings);
		settings.c_cc[VMIN]=1;
		settings.c_cc[VTIME]=10;
		cfsetspeed(&settings,baudRate);
		settings.c_cflag|=(wordSize|parity|outputFlowControl|inputFlowControl);
		if(tcsetattr(file,TCSANOW,&newSettings)==-1){
			result=NO;
			NSLog(@"%s error setting serial port settings (%d: \"%s\")",_cmd,errno,strerror(errno));
			}
		return result;
		}

//
//
// Public methods
//
//

	-(void)close{
		if(file!=-1){
		
			//
			// Drain whatever's left unread in the modem 
			//
		
			if(tcdrain(file)==-1){
				NSLog(@"%s error waiting for drain (%d: \"%s\")",_cmd,errno,strerror(errno));
				}
			else{
			
				//
				// Reset the serial port to its original state
				//
			
				if(tcsetattr(file,TCSANOW,&settings)==-1){
					NSLog(@"%s error resetting serial port to original state (%d: \"%s\")",_cmd,errno,strerror(errno));
					}
				}
			close(file);
			file=0;
			}
		}

	-(int)open{
		
		//
		// Get the path to the output device as an NSString (via CFString) string
		//
	
		CFTypeRef path=IORegistryEntryCreateCFProperty(modem,CFSTR(kIOCalloutDeviceKey),kCFAllocatorDefault,0);
        if(!path){
			NSLog(@"%s error retrieving path from IOKit modem device",self,_cmd);
			}
		else{
		
			//
			// Open the device for read/write, no TTY, non-blocking reads
			//
		
			file=open([(NSString*)path UTF8String],O_RDWR|O_NOCTTY|O_NONBLOCK);
			if(file==-1){
				NSLog(@"%s error opening serial port %s (%d: \"%s\")",_cmd,[(NSString*)path UTF8String],errno,strerror(errno));
				}
			else{
			
				//
				// Prevent others from accessing this device while we've got it open
				//

				if(ioctl(file,TIOCEXCL)==-1){
					NSLog(@"%s error acquiring exclusive access to serial port %s (%d: \"%s\")",_cmd,[(NSString*)path UTF8String],errno,strerror(errno));
					}
				else{
				
					//
					// DISABLED Now that we have exclusive access to the device,turn blocking IO back on
					//
	
					if(0){//fcntl(file,F_SETFL,0)==-1){
						NSLog(@"%s error activating blocking I/O %s (%d: \"%s\")",_cmd,[(NSString*)path UTF8String],errno,strerror(errno));
						}
					else{
					
						//
						// Get the port settings
						//

						if(tcgetattr(file,&settings)==-1){
							NSLog(@"%s error storing original serial port settings %s (%d: \"%s\")",_cmd,[(NSString*)path UTF8String],errno,strerror(errno));
							}
						else{
							if([self configureSerialPort]){
							
								//
								// Establish handshaking
								//
								
								if(ioctl(file,TIOCSDTR)==-1){
									NSLog(@"%s error asserting DTR %s (%d: \"%s\")",_cmd,[(NSString*)path UTF8String],errno,strerror(errno));
									}
								else{
									if(ioctl(file,TIOCCDTR)==-1){
										NSLog(@"%s error clearing DTR %s (%d: \"%s\")",_cmd,[(NSString*)path UTF8String],errno,strerror(errno));
										}
									else{
										int handshake=TIOCM_DTR|TIOCM_RTS|TIOCM_CTS|TIOCM_DSR;
										if(ioctl(file,TIOCMSET,&handshake)==-1){
											NSLog(@"%s error setting handshake lines %s (%d: \"%s\")",_cmd,[(NSString*)path UTF8String],errno,strerror(errno));
											}
										else{
											if(ioctl(file,TIOCMGET,&handshake)==-1){
												NSLog(@"%s error getting handshake lines %s (%d: \"%s\")",_cmd,[(NSString*)path UTF8String],errno,strerror(errno));
												}
											else{
												return YES;
												}
											}
										}
									}
								}
							}
						}
					}
				}
			CFRelease(path);
			}
			
		//
		// We only get here if something's failed above
		//
		
		if(file!=0){
			close(file);
			}
		return NO;
		}
		
	-(NSString*)responseToCommand:(NSString*)string{
	
		//
		// Ensure the command ends with a carriage return (and no other whitespace)
		//
		
		NSString* command=[NSString stringWithFormat:@"%@\r",[string stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]]];
		
		//
		// Try several times to send the command (in this case, three; it's arbitrary)
		//

		int attempt;
		const char* writeBuffer=[command UTF8String];
		int writeBufferSize=strlen(writeBuffer);
		for(attempt=1;attempt<=3;attempt++){
			int bytesWritten=write(file,writeBuffer,writeBufferSize);
			if(bytesWritten==-1){
				if(errno==35){
					NSLog(@"%s'%@': error writing to serial port (%d: \"%s\"); will retry after 250 microseconds",_cmd,string,errno,strerror(errno));
					usleep(250);
					continue;
					}
				else{
					NSLog(@"%s'%@': error writing to serial port (%d: \"%s\")",_cmd,string,errno,strerror(errno));
					return nil;
					}
				}
			else if (bytesWritten<writeBufferSize){
				continue;
				}
			
			//
			// After a successful write, read the response
			//
			
			NSMutableString* result=nil;
			#define readBufferSize (1024*1024*64)
			char* readBuffer=malloc(readBufferSize);
			bzero(readBuffer,readBufferSize);
			if(readBuffer){
				size_t bytesRead=0;
				int readBufferPosition=0;
				do{
					bytesRead=read(file,&readBuffer[readBufferPosition],readBufferSize-readBufferPosition-1);
					if(bytesRead==-1){
						if(errno==35){
							NSLog(@"%s'%@': error writing to serial port (%d: \"%s\"); will retry after 250 microseconds",_cmd,string,errno,strerror(errno));
							usleep(250);
							continue;
							}
						else{
							NSLog(@"%s'%@': error writing to serial port (%d: \"%s\")",_cmd,string,errno,strerror(errno));
							return nil;
							}
						}
					else if(bytesRead>0){
						readBufferPosition+=bytesRead;
						if(readBuffer[readBufferPosition-1]=='\n'||readBuffer[readBufferPosition-1]=='\r'){
							break;
							}
						}
					else{
						return @"";
						}
					}while(bytesRead>0);
				readBuffer[readBufferPosition-1]='\0';
				
				//
				// Clean up the result
				//
				
				result=[[[NSMutableString alloc] initWithBytes:readBuffer length:readBufferPosition encoding:NSUTF8StringEncoding] autorelease];
				[result replaceOccurrencesOfString:@"\r\n\r\n" withString:@"\r\n" options:0 range:NSMakeRange(0,[result length])];
				result=(NSMutableString*)[result stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]];;
				free(readBuffer);
				}
			return result;
			}
		return nil;
		}
	
	-(BOOL)connect{
		BOOL result=NO;
		if([self open]){
			NSString* response=[self responseToCommand:@"ATE0 V1"]; // You'll want to change this command, probably
			NSRange range=[response rangeOfString:@"OK"];
			result=(range.location!=NSNotFound);
			}
		return result;
		}
		
	-(void)disconnect{
		[self close];
		}
		
	-(id)initWithModem:(io_object_t)theModem{
		if(self=[super init]){
			modem=theModem;
			}
		return self;
		}

	-(NSString*)name{
		CFTypeRef name=0;
		io_object_t device=modem;
		while((!name)&&(device)){
			name=IORegistryEntryCreateCFProperty(device,CFSTR("Product Name"),kCFAllocatorDefault,0);
			if(!name){
				name=IORegistryEntryCreateCFProperty(device,CFSTR("BTName"),kCFAllocatorDefault,0);
				}
			IORegistryEntryGetParentEntry(device,kIOServicePlane,&device);
			}
		if(!name){
			name=IORegistryEntryCreateCFProperty(modem,CFSTR(kIOCalloutDeviceKey),kCFAllocatorDefault,0);
			}
		return [(NSString*)name autorelease];
		}
		
	+(NSArray*)scanForModems{
		NSMutableArray* result=[NSMutableArray array];
		
		//
		// Use IOKit iterators to find all BSD modems
		//
		
		kern_return_t status; 
		CFMutableDictionaryRef criteria;
		criteria=IOServiceMatching(kIOSerialBSDServiceValue);
		if(criteria!=NULL){
			CFDictionarySetValue(criteria,CFSTR(kIOSerialBSDTypeKey),CFSTR(kIOSerialBSDModemType)); // Note: kIOSerialBSDRS232Type seems to find PDA syncs, not modems
			io_iterator_t iterator; 
			status=IOServiceGetMatchingServices(kIOMasterPortDefault,criteria,&iterator);   
			if(status==KERN_SUCCESS){
				io_object_t modem;
				while(modem=IOIteratorNext(iterator)){
					[result addObject:[Modem alloc] initWithModem:modem] autorelease;
					}
				IOObjectRelease(iterator);
				}
			}
		return result;
		}

//
//
// NSObject methods
//
//

	-(void)dealloc{
		[self close];
		IOObjectRelease(modem);
		[super dealloc];
		}
			
@end

