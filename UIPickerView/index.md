---
layout: page
title: UIPickerView
---



Part of the iPhone General/UIKit framework. Defined in <General/UIKit/General/UIPickerView.h>

    
- (id) initWithFrame: (struct General/CGRect)rect
{
     General/UIPickerView *refreshPicker = General/[[UIPickerView alloc] initWithFrame: General/CGRectMake(0.0f, rect.size.height - 200.0f, 320.0f, 200.0f)];
     [refreshPicker setDelegate: self];
     [refreshPicker setSoundsEnabled: TRUE];

     General/UIPickerTable *_table = [refreshPicker createTableWithFrame: General/CGRectMake(0.0f, 0.0f, 320.0f, 200.0f)];
     [_table setAllowsMultipleSelection: FALSE];

     General/UITableColumn *_pickerCol = General/[[UITableColumn alloc] initWithTitle: @"Refresh" identifier:@"refresh" width: rect.size.width];

     [refreshPicker columnForTable: _pickerCol];
}

- (int) numberOfColumnsInPickerView:(General/UIPickerView*)picker
{
     // Number of columns you want (1 column is like in when clicking an <select /> in Safari, multi columns like a date selector)
     return 1;
}

- (int) pickerView:(General/UIPickerView*)picker numberOfRowsInColumn:(int)col
{
     // Rows of selection for each row (use a switch for multi cols)
     return 2;
}

- (General/UIPickerTableCell*) pickerView:(General/UIPickerView*)picker tableCellForRow:(int)row inColumn:(int)col
{
     General/UIPickerTableCell *cell = General/[[UIPickerTableCell alloc] initWithFrame: General/CGRectMake(0.0f, 0.0f, 320.0f, 32.0f)];

     switch (row)
     {
          case 0:
		[cell setTitle: @"Manually"];
	  break;
	  case 1:
		[cell setTitle: @"5 min"];
	  break;
     }

     return cell;
}


How can I set the row that should be selected as default?
