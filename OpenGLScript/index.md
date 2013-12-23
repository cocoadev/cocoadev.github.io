---
layout: page
title: OpenGLScript
---




*MotivationForOpenGLScript 
*CurrentSyntaxAndRequests


OK here's an initial implementation of OpenGLScript. Please be kind with the comments because I'm not an alpha geek (more like a beta geek). At the momment you have to make the last line of the script the entry EOF for end of file.

----
I just added a separate window that will allow you to edit single elements without having to save and reload the script file. Follow the directions below to start working with UmGLScript (You need to add two classes to Lesson07 which involves creating four source files (UmGLScript.h, UmGLScript.m, UmGLScriptEditor.h, UmGLScriptEditor.m). The source code to make these four files is in the links below.

Click on the Edit View tab to edit single parameters. The OpenGLView should update automatically after you hit "RETURN" when you edit individual text cells in the table view.

Take note of the modification you have to do to Lesson07 to get the scripter to display a separate edit window.
----
Now you can use the Editor View to load scripts by pasting text directly into it. Just add another menu item in IB that can generate an IBAction with the name "runScript". Then modify Lesson07Controller and Lesson07View so they can pass the menu item event to the UmGLScript instance. (I'll write up better instructions soon).


note: botth UmGLScript and UmGLScriptEditor have been updated (02/09/03 10:52pm eastern)
----


*UmGLScript
*UmGLScriptEditor <- added 02/09/03 (see comments about change)


The class UmGLScript has three instance methods of interest so far:

    
-(BOOL)loadScriptAtPath:(NSString *)path;
-(void)runScript;
-(void)performScript;



loadScriptAtPath is the method to load an OpenGL script. The documentation is coming soon. I just posted this for those who like to tinker. The syntax is just like plan OpenGL calls. The following will work with this interpreter.

    
    glLoadIdentity();   // Reset the current modelview matrix

   glTranslatef( 0.0f, 0.0f, -8 );   // In/out of screen by zPos
   // Rotate on X axis
   glRotatef( 20, 1.0f, 0.0f, 0.0f );
   // Rotate on Y axis
   glRotatef( 20, 0.0f, 1.0f, 0.0f );
   glBegin( GL_QUADS ); 
   // Front Face
   glNormal3f( 0.0f, 0.0f, 1.0f );      // Normal Pointing Towards Viewer
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f,  1.0f );   // Point 1 (Front) 
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f,  1.0f );   // Point 2 (Front)
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f,  1.0f );   // Point 3 (Front)
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f,  1.0f );   // Point 4 (Front)
   // Back Face
   glNormal3f( 0.0f, 0.0f, -1.0f );     // Normal Pointing Away From Viewer
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f, -1.0f );   // Point 1 (Back)
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f, -1.0f );   // Point 2 (Back)
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f, -1.0f );   // Point 3 (Back)
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f, -1.0f );   // Point 4 (Back)
   // Top Face
   glNormal3f( 0.0f, 1.0f, 0.0f );      // Normal Pointing Up
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f, -1.0f );   // Point 1 (Top)
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f( -1.0f,  1.0f,  1.0f );   // Point 2 (Top)
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f(  1.0f,  1.0f,  1.0f );   // Point 3 (Top)
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f, -1.0f );   // Point 4 (Top)
   // Bottom Face
   glNormal3f( 0.0f, -1.0f, 0.0f );     // Normal Pointing Down
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f( -1.0f, -1.0f, -1.0f );   // Point 1 (Bottom)
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f(  1.0f, -1.0f, -1.0f );   // Point 2 (Bottom)
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f,  1.0f );   // Point 3 (Bottom)
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f,  1.0f );   // Point 4 (Bottom)
   // Right face
   glNormal3f( 1.0f, 0.0f, 0.0f);       // Normal Pointing Right
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f, -1.0f );   // Point 1 (Right)
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f, -1.0f );   // Point 2 (Right)
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f(  1.0f,  1.0f,  1.0f );   // Point 3 (Right)
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f(  1.0f, -1.0f,  1.0f );   // Point 4 (Right)
   // Left Face
   glNormal3f( -1.0f, 0.0f, 0.0f );     // Normal Pointing Left
   glTexCoord2f( 0.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f, -1.0f );   // Point 1 (Left)
   glTexCoord2f( 1.0f, 0.0f );
   glVertex3f( -1.0f, -1.0f,  1.0f );   // Point 2 (Left)
   glTexCoord2f( 1.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f,  1.0f );   // Point 3 (Left)
   glTexCoord2f( 0.0f, 1.0f );
   glVertex3f( -1.0f,  1.0f, -1.0f );   // Point 4 (Left)
   glEnd();                             // Done Drawing Quads
   EOF




Copy, paste and save the script above into a file with a path something like "/Users/yourHomeDir/glScript". 

If you go to http://www.withay.com/macosx/opengl/  and download Lesson07 you can start to play with UmGLScript. Just build the project for Lesson07 to see what the box looks like with the texture maps. Then if everything bulds OK, modify the Lesson07View.m file by doing the following:


*delete the lines in "drawRect:" that start with glBegin( GL_QUADS ); and end with glEnd();
* now add the following line in drawRect in the place that the deleted OpenGL drawing code used to exist at
    
[scripter performScript];

* (NEW) add an instance variable to Lesson07.h of type UmGLScript  (i.e. UmGLScript *scripter) and import the class UmGLScript (Dont forget to create and add the files for UmGLScript and UmGLScriptEditor to the project).
* alloc and init an instance of UmGLScript in the following method:
    
- (id) initWithFrame:(NSRect)frame colorBits:(int)numColorBits
       depthBits:(int)numDepthBits fullscreen:(BOOL)runFullScreen [
 
      scripter=[[UmGLScript alloc] initWithFrame:NSMakeRect(100,100,800,600)]; // <- new!!
      [scripter loadScriptAtPath:@"/Users/yourHomeDir/glScript"];  // <- not that important now



* reference the new instance with the instance variable you added to the header file
* provide a path to a script file (i.e. cut and paste the example script above into a file named glScript
* Now here's the fun part. Once you have everything working, add a menu item in IB called "Reload Script". 
*Make the right connections and modify Lesson07View so that you can send a -(BOOL)loadScriptAtPath: message with this IBAction. (The details will come SOON). 
*Now add the glScript file (that you saved at the path /Users/yourHomeDir/glScript) to the project. You can now edit this script file and save it in PB. After you have made and saved minor changes you can just click on the running app and reload the script to see how your small changes look.


Hopefully everything works, please let me know about all bugs. I'm sure there are some lurking around since I just got this code working about an hour ago.

I'm also almost finished with the OpenGLImageCapture class that will let you take snap shots of a OpenGL view into an NSImageRep so you can do fun compositing things with other NSImages or any NSView.

--zootbobbalu

