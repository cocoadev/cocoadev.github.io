---
layout: page
title: ProblemsAboutRetainCount
---

Describe ProblemsAboutRetainCount here.



    

#import <Foundation/Foundation.h>

int main (int argc, const char * argv[]) {
	NSMutableArray *array;
	int i,j;
	NSNumber *newNumber;
	
	array = [[NSMutableArray alloc] init];
	j = 0;
	for(i = 0;i < 10;i++)
	{
		NSLog(@"-----------%d----------",i);
		newNumber = [[NSNumber alloc] initWithInt:i*3];
		j = [newNumber retainCount];
		NSLog(@"before retain count %d",j);

		[array addObject:newNumber];
		j = [newNumber retainCount];
		NSLog(@"middle retain count %d",j);

		[newNumber release];
		j = [newNumber retainCount];
		NSLog(@"after retain count %d",j);
	}
    return 0;
}


�?�?�?�?�?result�?�?�?�?�?�?�?�?�?
    
-----------0----------
before retain count 2
middle retain count 3
after retain count 2
-----------1----------
before retain count 2
middle retain count 3
after retain count 2
-----------2----------
before retain count 2
middle retain count 3
after retain count 2
-----------3----------
before retain count 2
middle retain count 3
after retain count 2
-----------4----------
before retain count 2
middle retain count 3
after retain count 2
-----------5----------
before retain count 1
middle retain count 2
after retain count 1
-----------6----------
before retain count 1
middle retain count 2
after retain count 1
-----------7----------
before retain count 1
middle retain count 2
after retain count 1
-----------8----------
before retain count 1
middle retain count 2
after retain count 1
-----------9----------
before retain count 1
middle retain count 2
after retain count 1



I'm a cocoa beginner . When I see something about memory treatment ,I made the experiment.
I think the result should be

    

-----------0----------
before retain count 1
middle retain count 2
after retain count 1
-----------1----------
before retain count 1
middle retain count 2
after retain count 1
-----------2----------
before retain count 1
middle retain count 2
after retain count 1
-----------3----------
before retain count 1
middle retain count 2
after retain count 1
-----------4----------
before retain count 1
middle retain count 2
after retain count 1
-----------5----------
before retain count 1
middle retain count 2
after retain count 1
-----------6----------
before retain count 1
middle retain count 2
after retain count 1
-----------7----------
before retain count 1
middle retain count 2
after retain count 1
-----------8----------
before retain count 1
middle retain count 2
after retain count 1
-----------9----------
before retain count 1
middle retain count 2
after retain count 1


or
    

-----------0----------
before retain count 2
middle retain count 3
after retain count 2
-----------1----------
before retain count 2
middle retain count 3
after retain count 2
-----------2----------
before retain count 2
middle retain count 3
after retain count 2
-----------3----------
before retain count 2
middle retain count 3
after retain count 2
-----------4----------
before retain count 2
middle retain count 3
after retain count 2
-----------5----------
before retain count 2
middle retain count 3
after retain count 2
-----------6----------
before retain count 2
middle retain count 3
after retain count 2
-----------7----------
before retain count 2
middle retain count 3
after retain count 2
-----------8----------
before retain count 2
middle retain count 3
after retain count 2
-----------9----------
before retain count 2
middle retain count 3
after retain count 2


can anyone  tell me why i'm wrong??

----

You are seeing an Apple optimization in action. Apple is reusing NSNumber objects. You should log this.

    

@interface CustomNumberObject : NSObject {
	int value;
}
@end

@implementation CustomNumberObject
- (id)initWithInt:(int)val {
	if (self = [super init]) {
		value = val;
	}
	return self;
}
- (NSString *)description {
	return [NSString stringWithFormat:@"%i", value];
}

@end


int main (int argc, const char * argv[]) {

	NSAutoreleasePool *innerPool = [[NSAutoreleasePool alloc] init];
	
	NSMutableArray *array = [NSMutableArray array];
	int i;
	NSNumber *newNumber;
	CustomNumberObject *customNumber;
		
	for (i = 0; i < 10; i++) {
		NSLog(@"----------- NSNumber %d ----------", i);
		newNumber = [[NSNumber alloc] initWithInt:i % 3];
		NSLog(@"before NSNumber: \"%2@\" <%p|%i>", newNumber, newNumber, [newNumber retainCount]);

		[array addObject:newNumber];
		NSLog(@"middle NSNumber: \"%2@\" <%p|%i>", newNumber, newNumber, [newNumber retainCount]);

		[newNumber release];
		NSLog(@"after  NSNumber: \"%2@\" <%p|%i>", newNumber, newNumber, [newNumber retainCount]);
	}

	for (i = 0; i < 10; i++) {
		NSLog(@"----------- CustomNumberObject %d ----------", i);
		customNumber = [[CustomNumberObject alloc] initWithInt:i % 3];
		NSLog(@"before CustomNumberObject: \"%2@\" <%p|%i>", customNumber, customNumber, [customNumber retainCount]);

		[array addObject:customNumber];
		NSLog(@"middle CustomNumberObject: \"%2@\" <%p|%i>", customNumber, customNumber, [customNumber retainCount]);

		[customNumber release];
		NSLog(@"after  CustomNumberObject: \"%2@\" <%p|%i>", customNumber, customNumber, [customNumber retainCount]);
	}

	[innerPool release];

    return 0;
}



This outputs this
    
2006-07-04 09:19:07.911 test[5345] ----------- NSNumber 0 ----------
2006-07-04 09:19:07.919 test[5345] before NSNumber: "0" <0x3072a0|2>
2006-07-04 09:19:07.920 test[5345] middle NSNumber: "0" <0x3072a0|3>
2006-07-04 09:19:07.920 test[5345] after  NSNumber: "0" <0x3072a0|2>
2006-07-04 09:19:07.920 test[5345] ----------- NSNumber 1 ----------
2006-07-04 09:19:07.920 test[5345] before NSNumber: "1" <0x3083b0|2>
2006-07-04 09:19:07.920 test[5345] middle NSNumber: "1" <0x3083b0|3>
2006-07-04 09:19:07.920 test[5345] after  NSNumber: "1" <0x3083b0|2>
2006-07-04 09:19:07.920 test[5345] ----------- NSNumber 2 ----------
2006-07-04 09:19:07.920 test[5345] before NSNumber: "2" <0x307420|2>
2006-07-04 09:19:07.920 test[5345] middle NSNumber: "2" <0x307420|3>
2006-07-04 09:19:07.920 test[5345] after  NSNumber: "2" <0x307420|2>
2006-07-04 09:19:07.920 test[5345] ----------- NSNumber 3 ----------
2006-07-04 09:19:07.920 test[5345] before NSNumber: "0" <0x3072a0|3>
2006-07-04 09:19:07.921 test[5345] middle NSNumber: "0" <0x3072a0|4>
2006-07-04 09:19:07.921 test[5345] after  NSNumber: "0" <0x3072a0|3>
2006-07-04 09:19:07.921 test[5345] ----------- NSNumber 4 ----------
2006-07-04 09:19:07.921 test[5345] before NSNumber: "1" <0x3083b0|3>
2006-07-04 09:19:07.921 test[5345] middle NSNumber: "1" <0x3083b0|4>
2006-07-04 09:19:07.921 test[5345] after  NSNumber: "1" <0x3083b0|3>
2006-07-04 09:19:07.921 test[5345] ----------- NSNumber 5 ----------
2006-07-04 09:19:07.921 test[5345] before NSNumber: "2" <0x307420|3>
2006-07-04 09:19:07.921 test[5345] middle NSNumber: "2" <0x307420|4>
2006-07-04 09:19:07.921 test[5345] after  NSNumber: "2" <0x307420|3>
2006-07-04 09:19:07.923 test[5345] ----------- NSNumber 6 ----------
2006-07-04 09:19:07.923 test[5345] before NSNumber: "0" <0x3072a0|4>
2006-07-04 09:19:07.923 test[5345] middle NSNumber: "0" <0x3072a0|5>
2006-07-04 09:19:07.923 test[5345] after  NSNumber: "0" <0x3072a0|4>
2006-07-04 09:19:07.923 test[5345] ----------- NSNumber 7 ----------
2006-07-04 09:19:07.923 test[5345] before NSNumber: "1" <0x3083b0|4>
2006-07-04 09:19:07.923 test[5345] middle NSNumber: "1" <0x3083b0|5>
2006-07-04 09:19:07.924 test[5345] after  NSNumber: "1" <0x3083b0|4>
2006-07-04 09:19:07.924 test[5345] ----------- NSNumber 8 ----------
2006-07-04 09:19:07.924 test[5345] before NSNumber: "2" <0x307420|4>
2006-07-04 09:19:07.924 test[5345] middle NSNumber: "2" <0x307420|5>
2006-07-04 09:19:07.924 test[5345] after  NSNumber: "2" <0x307420|4>
2006-07-04 09:19:07.924 test[5345] ----------- NSNumber 9 ----------
2006-07-04 09:19:07.924 test[5345] before NSNumber: "0" <0x3072a0|5>
2006-07-04 09:19:07.924 test[5345] middle NSNumber: "0" <0x3072a0|6>
2006-07-04 09:19:07.924 test[5345] after  NSNumber: "0" <0x3072a0|5>
2006-07-04 09:19:07.924 test[5345] ----------- CustomNumberObject 0 ----------
2006-07-04 09:19:07.924 test[5345] before CustomNumberObject: "0" <0x307490|1>
2006-07-04 09:19:07.925 test[5345] middle CustomNumberObject: "0" <0x307490|2>
2006-07-04 09:19:07.925 test[5345] after  CustomNumberObject: "0" <0x307490|1>
2006-07-04 09:19:07.925 test[5345] ----------- CustomNumberObject 1 ----------
2006-07-04 09:19:07.925 test[5345] before CustomNumberObject: "1" <0x308900|1>
2006-07-04 09:19:07.925 test[5345] middle CustomNumberObject: "1" <0x308900|2>
2006-07-04 09:19:07.925 test[5345] after  CustomNumberObject: "1" <0x308900|1>
2006-07-04 09:19:07.925 test[5345] ----------- CustomNumberObject 2 ----------
2006-07-04 09:19:07.925 test[5345] before CustomNumberObject: "2" <0x308910|1>
2006-07-04 09:19:07.925 test[5345] middle CustomNumberObject: "2" <0x308910|2>
2006-07-04 09:19:07.925 test[5345] after  CustomNumberObject: "2" <0x308910|1>
2006-07-04 09:19:07.925 test[5345] ----------- CustomNumberObject 3 ----------
2006-07-04 09:19:07.925 test[5345] before CustomNumberObject: "0" <0x3089b0|1>
2006-07-04 09:19:07.925 test[5345] middle CustomNumberObject: "0" <0x3089b0|2>
2006-07-04 09:19:07.926 test[5345] after  CustomNumberObject: "0" <0x3089b0|1>
2006-07-04 09:19:07.926 test[5345] ----------- CustomNumberObject 4 ----------
2006-07-04 09:19:07.926 test[5345] before CustomNumberObject: "1" <0x308840|1>
2006-07-04 09:19:07.926 test[5345] middle CustomNumberObject: "1" <0x308840|2>
2006-07-04 09:19:07.926 test[5345] after  CustomNumberObject: "1" <0x308840|1>
2006-07-04 09:19:07.926 test[5345] ----------- CustomNumberObject 5 ----------
2006-07-04 09:19:07.926 test[5345] before CustomNumberObject: "2" <0x3089c0|1>
2006-07-04 09:19:07.926 test[5345] middle CustomNumberObject: "2" <0x3089c0|2>
2006-07-04 09:19:07.926 test[5345] after  CustomNumberObject: "2" <0x3089c0|1>
2006-07-04 09:19:07.926 test[5345] ----------- CustomNumberObject 6 ----------
2006-07-04 09:19:07.926 test[5345] before CustomNumberObject: "0" <0x308920|1>
2006-07-04 09:19:07.926 test[5345] middle CustomNumberObject: "0" <0x308920|2>
2006-07-04 09:19:07.927 test[5345] after  CustomNumberObject: "0" <0x308920|1>
2006-07-04 09:19:07.927 test[5345] ----------- CustomNumberObject 7 ----------
2006-07-04 09:19:07.927 test[5345] before CustomNumberObject: "1" <0x308930|1>
2006-07-04 09:19:07.927 test[5345] middle CustomNumberObject: "1" <0x308930|2>
2006-07-04 09:19:07.927 test[5345] after  CustomNumberObject: "1" <0x308930|1>
2006-07-04 09:19:07.927 test[5345] ----------- CustomNumberObject 8 ----------
2006-07-04 09:19:07.927 test[5345] before CustomNumberObject: "2" <0x308940|1>
2006-07-04 09:19:07.927 test[5345] middle CustomNumberObject: "2" <0x308940|2>
2006-07-04 09:19:07.927 test[5345] after  CustomNumberObject: "2" <0x308940|1>
2006-07-04 09:19:07.927 test[5345] ----------- CustomNumberObject 9 ----------
2006-07-04 09:19:07.927 test[5345] before CustomNumberObject: "0" <0x308800|1>
2006-07-04 09:19:07.927 test[5345] middle CustomNumberObject: "0" <0x308800|2>
2006-07-04 09:19:07.927 test[5345] after  CustomNumberObject: "0" <0x308800|1>



The "i % 3" operation will set repeating values every third iteration. Notice the pointer values for newNumber objects when "i" equals (0, 3, 6, 9) are the same. This is the result of Apple reusing number objects that already exists. "NSNumber" is probably caching popular int values (i.e. int values in a certain range could be saved for later use). Exactly what they are doing should not be a concern to you. --zootbobbalu

----

Indeed, retain counts should not be a concern to you. Learn the (simple) rules about when to retain and when not to, and you'll be fine.

----
I'm very pleased that I get the response so quickly.In my experiment , I used i*3 not i%3.After watching the fragment of your program, I try to use " NSLog(@"before NSNumber: \"%2@\" <%p|%i>", newNumber, newNumber, [newNumber retainCount]); ",and I get the result like this
    

 -----------0----------
 before NSNumber: "0" <0x303200|2>
 middle NSNumber: "0" <0x303200|3>
 middle NSNumber: "0" <0x303200|2>
 -----------1----------
 before NSNumber: "3" <0x305020|2>
 middle NSNumber: "3" <0x305020|3>
 middle NSNumber: "3" <0x305020|2>
 -----------2----------
 before NSNumber: "6" <0x303b00|2>
 middle NSNumber: "6" <0x303b00|3>
 middle NSNumber: "6" <0x303b00|2>
 -----------3----------
 before NSNumber: "9" <0x303ae0|2>
 middle NSNumber: "9" <0x303ae0|3>
 middle NSNumber: "9" <0x303ae0|2>
 -----------4----------
 before NSNumber: "12" <0x303af0|2>
 middle NSNumber: "12" <0x303af0|3>
 middle NSNumber: "12" <0x303af0|2>
 -----------5----------
 before NSNumber: "15" <0x305450|1>
 middle NSNumber: "15" <0x305450|2>
 middle NSNumber: "15" <0x305450|1>
 -----------6----------
 before NSNumber: "18" <0x303a90|1>
 middle NSNumber: "18" <0x303a90|2>
 middle NSNumber: "18" <0x303a90|1>
 -----------7----------
 before NSNumber: "21" <0x303aa0|1>
 middle NSNumber: "21" <0x303aa0|2>
 middle NSNumber: "21" <0x303aa0|1>
 -----------8----------
 before NSNumber: "24" <0x305480|1>
 middle NSNumber: "24" <0x305480|2>
 middle NSNumber: "24" <0x305480|1>
 -----------9----------
 before NSNumber: "27" <0x305440|1>
 middle NSNumber: "27" <0x305440|2>
 middle NSNumber: "27" <0x305440|1>


Question 1:
I can't understand that why newNumber's first retainCount is 2?
Question 2:
Why the retainCount in the 6th iteration become 1?

The Reference just said that you alloc the NSNumber , the retainCount increased by 1
and an array send the addObject message, and the retainCount increased by 1.
So now the retainCount is 2. when I release the newNumber ,the retainCount decreased by 1.
------Daniel

