---
layout: page
title: NSUndoTextField
---

NSUndoManager for Textfields. When you have a textField in a documenet based application, it automatically provides undo & redo when you change the text in textField. However it doesn't show that the document has been edited. The edit would invoke an edit notifcation in the close button of the window(the little dot that appears in the red close button). If you have a setter and getter method tied to that textfield. What extra code is required to invokes the edit function showing the window has been edited. eg

    
-(NSString *)myName
{
    return myName;
}

-(void)setMyName:(NSString *)aName
{
   aName =[aName copy];
  [myName release];
  myName =aName;
}

I tried the the following and it seems to work ok, except when I save and close the document and reopen it, it show as being edited.
    
-(NSString *)myName
{
    return myName;
}

-(void)setMyName:(NSString *)aName
{
    NSUndoManager *undoManager - [self undoManager];
    [[undo prepareWithInvocationTarget: self] setMyName: myName];
     if(![undo isUndoing]){
      [undo setActionName:@"Edit"];

   aName =[aName copy];
  [myName release];
  myName =aName;
}

DSG

