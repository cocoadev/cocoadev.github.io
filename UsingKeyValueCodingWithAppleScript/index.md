---
layout: page
title: UsingKeyValueCodingWithAppleScript
---

I am trying to enable Applescripting in an Application I'm working on.  But I can't seem to get it to work.  When I access a variable with General/AppleScript in my application, I get the following error:

General/AppleScript Error:
<class> got an error: Can't make <key> into type reference.

I'm at a loss as to why I can't access this variable.  Any help would be greatly appreciated.  Thank you to Dustin Voss for your General/HowToSupportAppleScript, I found it very useful.

Here is my *.sdef file:
    
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE dictionary SYSTEM "file://localhost/System/Library/General/DTDs/sdef.dtd">
<dictionary title="General/SomeClass">
	<suite name="General/SomeClass AS" code="NBAS" description="Apple Srcipting for General/SomeClass">
		<cocoa name="General/SomeClass"/>
		<class name="General/SomeClass" code="btdv" description="the Class of a given General/SomeClass device" plural="General/SomeClasses">
			<cocoa class="General/SomeClass"/>
			<property name="tempX" code="tmpx" description="dummy variable" type="integer" access="r">
				<cocoa key="_tempX"/>
			</property>
			<property name="vernum" code="vrsn" description="version of device" type="integer" access="r">
				<cocoa key="vernum"/>
			</property>
		</class>
	</suite>
</dictionary>


Here is the KVC of the cocoa key for vernum:

    
UInt32   vernum;

- (UInt32)vernum {
    return [[vernum retain] autorelease];
}

- (void)setVernum:(UInt32)value {
    if (vernum != value) {
        [vernum release];
        vernum = [value copy];
    }
}


----

UInt32 is a primitive, not a class.

    
- (UInt32)vernum {
    return vernum;
}

- (void)setVernum:(UInt32)value {
        vernum = value;
}

