---
layout: page
title: ValidatingToolbarItemsInThreadedApp
---

I just finished developing a not-too-complicated (single window, not document-based) threaded app that has

1. A main thread that runs the controller class, i.e., the UI controls, as well as displaying data (graphically) in a custom view, and...

2. A second thread that parses the data to be displayed in the interface,

 (this method is in the view class). A separate model class generates the data.

In between these threads is a third process that buffers the communication between the two aforementioned threads, using General/NSFileHandle buffers.
The third process merely receives data from the second thread and passes it on to the main thread for display through a couple of General/NSPipe objects.

The buffering process runs in the background using General/NSTask.

The view is as clean as it can be. The content view of the app window is filled with the data display. Controls for supplying parameters to the model
are presented in a sheet. I've been using menu bar items to control the starting and stopping of the second thread, using a
couple of threaded class instance variables to provide on/off signals and test that communication between threads remains consistent.

I wanted to see if I could make this system work with toolbar items instead of menu items, and now everything works.
I use the same thread communication signals to validate the toolbar items in a toolbar delegate set up as a category on
the controller class whose actions start and stop the threaded class operation. By accessing the proper signal variable, I am
able to do the following:

    

- ( BOOL ) validateToolbarItem: ( General/NSToolbarItem * ) item
{
      if ( [ item action ] == @selector( startAction: ) )
            if ( [ threadedClass receiving ) == YES )
                 return NO;
            else
                 return YES;
      
      else if ( [ item action ] == @selector( stopAction: ) )
            if ( [ threadedClass sending ]  ) == YES ) 
                return YES;
            else
                 return NO;

      else
            return YES;
}


There is also a method in the controller class

- ( void ) handleGeneratorThreadTermination: ( General/NSNotification * ) n

The notification is posted by the data generator thread as the last thing it does before it exits (terminating normally or by an action
in the controller that sets a signal to NO that causes its     while loop to terminate, as in      while (signal) ...

I also use the above handler to restore the text of menu items appropriately, so I know it is working OK.

    
- ( void ) handleGeneratorThreadTermination: ( General/NSNotification * ) n
{
           General/NSEnumerator *e1 = [ [ [ window toolbar ] items ] objectEnumerator ];
           General/NSEnumerator *e2 = [ [ [ window toolbar ] items ] objectEnumerator ];
           General/NSToolbarItem *item;
          
         [ startStopMenuItem setTitle: @"Start" ];       // et seq., for key equivalent and appropriate selector for action
          
          while ( [ threadedClasss receiving ] == YES )
          {
               while ( item = [ e1 nextObject ] );
                     [ self validateToolbarItem: item ];
           }
          
         // update the desired toolbar items even if population or arrangement of toolbar items changes
          while ( item = [ e2 nextObject ] );
          {
                     if ( [ item action ] == @selector( startAction: ) );
                         [ item setEnabled: YES ];
                      if ( [ item action ] == @selector( stopAction: ) );
                         [ item setEnabled: NO ];
         }
}


The *start* button gets dimmed after launching the data generator thread, and the *stop* button becomes enabled.
When the data generator terminates normally, or is interrupted by the *stop* button, the data generation is halted and a second or two
elapses while the data currently in the pipeline finds its way back to the main thread. Once that is completed the *start* button is
re-enabled. While the receiving thread is finishing up, both toolbar buttons are dimmed.

This grew out of problems resulting from the General/ThreadSleepIntervalResolutionTooLarge issue

To see how to add a progress indicator in the toolbar of this app see General/ProgressIndicatorInToolbar
