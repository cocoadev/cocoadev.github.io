---
layout: page
title: ComputeSumOfTableViewColumnValues
---

I've got a basic NSTableView, but I want to add all the values of one column and put it in a text field. I'm stumped. Is there an easy way to do this?
----
    

-(IBAction)addButton:(id)sender {
    int i;
    int sum=0; 
    for (i=0;i<[tableData count];i++) {
        sum+=[tableData objectAtIndex:i] objectForKey:@"keyForColumnYouWantToSum"] intValue];
    }
    [whatEverOutletYouWantToDisplayYourResultTo setIntValue:sum];
}


Better yet

    
-(void)awakeFromNib {
    [tableView setAllowsColumnSelection:YES];
}


-([[IBAction)addButton:(id)sender {
    int i;
    int sum=0; 
    int selectedColumn = [tableView selectedColumn];
    id columns = [tableView tableColumns];
    id columnSelected = nil;
    if ( selectedColumn > -1 ) columnSelected = [columns objectAtIndex:selectedColumn];
    else return;
    id columnID = [columnSelected identifier];
    for ( i=0 ; i<[tableData count] ; i++ ) {
        sum += [tableData objectAtIndex:i] objectForKey:columnID] intValue];
    }
    [whatEverOutletYouWantToDisplayYourResultTo setIntValue:sum];

}


----

Seems to work fine except for one tiny error: it doesn't return anything after the decimal place. So if I have 5, 6, and 1.5, it will return 12, not 12.5. Any ideas?

----
You are using float values. Just change the type for "sum" from "int" to type "float" and change the value that gets added to sum from intValue to floatValue.

    
-(void)awakeFromNib {
    [tableView setAllowsColumnSelection:YES];
}


-([[IBAction)addButton:(id)sender {
    int i;
    float sum=0; 
    int selectedColumn = [tableView selectedColumn];
    id columns = [tableView tableColumns];
    id columnSelected = nil;
    if ( selectedColumn > -1 ) columnSelected = [columns objectAtIndex:selectedColumn];
    else return;
    id columnID = [columnSelected identifier];
    for ( i=0 ; i<[tableData count] ; i++ ) {
        sum += [tableData objectAtIndex:i] objectForKey:columnID] floatValue];
    }
    [whatEverOutletYouWantToDisplayYourResultTo setFloatValue:sum];

}


or for the simple case:

    

-([[IBAction)addButton:(id)sender {
    int i;
    float sum=0; 
    for (i=0;i<[tableData count];i++) {
        sum+=[tableData objectAtIndex:i] objectForKey:@"keyForColumnYouWantToSum"] floatValue];
    }
    [whatEverOutletYouWantToDisplayYourResultTo setFloatValue:sum];
}



I am a beginner. I tried implementing something like this in my code with no success. I linked my [[NSTextField with myDataSource Object that uses NSTextField. I also linked the Add button to the addButton function. No success. can someone explain the variables above? For example, in myDadSource file I have declared NSMutableArray * items in the header and a pointer to my object that handles all the data inside my textView as  table. When I insert these pointers into the code above I get

    
-(void)awakeFromNib {
    [table setAllowsColumnSelection:YES];
}


-(IBAction)addButton:(id)sender {
    int i;
    float sum=0; 
    int selectedColumn = [table selectedColumn];
    id columns = [table tableColumns];
    id columnSelected = nil;
    if ( selectedColumn > -1 ) columnSelected = [columns objectAtIndex:selectedColumn];
    else return;
    id columnID = [columnSelected identifier];
    for ( i=0 ; i<[items count] ; i++ ) {
        sum += [items objectAtIndex:i] objectForKey:columnID] floatValue];
    }
    [addField setFloatValue:sum];

}


When I do this and compile my application, Nothing happens to my [[NSTextField. What am I missing. Really frustrating you know!

I figured out my problem. it seams within the for loop whenever I wanted to use "objectForKey:columnID" I always got selector not recognized. When I changed it to "valueForKey" it summed up in my NSTextField! Sweet!

