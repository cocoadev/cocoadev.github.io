---
layout: page
title: LSHOMTest
---



A test function for LSTrampoline.

    
void LSHOMTest(void)
{
	NSArray *one = [@"one two three four five six seven eight nine ten" componentsSeparatedByString:@" "];
	NSArray *two = one select] hasPrefix:@"t"];
	[[NSArray *three = two reject] hasSuffix:@"e"];
	[[NSArray *four = three collect] stringByAppendingString:@" potato"];
	[[NSArray *five = one collect] mutableCopy];
	[[five do] appendString:@"_vegetable"];
	
	[[NSLog(@"%@", one);
	NSLog(@"%@", two);
	NSLog(@"%@", three);
	NSLog(@"%@", four);
	NSLog(@"%@", five);
}


I ran this just now, and here is the output:

    
2003-02-25 02:46:05.669 Lodestone[1360] (one, two, three, four, five, six, seven, eight, nine, ten)
2003-02-25 02:46:05.706 Lodestone[1360] (two, three, ten)
2003-02-25 02:46:05.707 Lodestone[1360] (two, ten)
2003-02-25 02:46:05.707 Lodestone[1360] ("two potato", "ten potato")
2003-02-25 02:46:05.708 Lodestone[1360] (
    "one_vegetable", 
    "two_vegetable", 
    "three_vegetable", 
    "four_vegetable", 
    "five_vegetable", 
    "six_vegetable", 
    "seven_vegetable", 
    "eight_vegetable", 
    "nine_vegetable", 
    "ten_vegetable"
)


-- RobRix

