---
layout: page
title: PicHandle
---

 Please help me
  How to get PicHandle
  I  can get image Width, Height,Resolution&image data Buffer.But I don't know How to get "PicHandle".
  I do it in this way
----
    

//******************************************************************************//
2003-10-5
GetImageHandle  
Return   PicHandle
//****************************************************************************'//

-(PicHandle)GetImageHandle{
   
 PicHandle hSMPicHandle;
 hSMPicHandle=nil;
 unsigned Picsize;

// 24bit color picture
Picsize= ImageHeight* ImageWidth*3 ; // Get image size;

// imageBlock.pBuffer:  The memory address that holds the image data.
//get image data
DS_RETURNCODE  result = [[SMClient shareSMClient ] SMQueryBlock:&imageBlock];       
if (result != DSRC_SCANDONE && result != DSRC_SCANMORE) 
     { 
        NSLog(@"QueryBlock failed. result = %d",result); 
        return  nil;  
     }

OpenCPicParams Parameter;
PicHandle TempPicHandle;
Handle dataHandle;

//Init Parameter
[self SetupOpenCPicture:&Parameter];

dataHandle= NewHandle(Picsize+sizeof(PicHandle));

TempPicHandle= OpenCPicture(&Parameter);
/*
// (**TemPicHandle).picSize=40 is error
//(**TemPicHandle).picFrame.top=840 is error
//
//I thank (**TemPicHandle).picFrame values should equal to
 Paramteter->srcRect.top
 Paramteter ->srcRect.left
 Paramteter ->srcRect.bottom
 Paramteter ->srcRect.right
*/ 

// move Header information to dataHandle
BlockMoveData(*TempPicHandl,*dataHandle,sizeof(PicHandle));  

// move image data to dataHandle; 
BlockMoveData(imageBlock.pBuffer,*dataHandle+sizeo(PicHandle ),Picsize);
        
hSMPicHandle=(PicHandle)dataHandle;

return hSMPicHandle;
}



//******************************************************************************//
2003-10-5
SetupOpenCPicture  Init. "OpenCPicParams" structure
Return   NULL
//****************************************************************************'//

  -(void) SetupOpenCPicture:(OpenCPicParams*) pOpenCPara{
 pOpenCPara->srcRect.top=0;
 pOpenCPara->srcRect.left=0;
 pOpenCPara->srcRect.bottom=(short) Image.Height;//1170
 pOpenCPara->srcRect.right=(short) Image.Width;//840
 pOpenCPara->hRes= Image.xResolution; //150
 pOpenCPara->vRes= Image.yResolution;//150
 pOpenCPara->version=-2;
 pOpenCPara->reserved1=0;
 pOpenCPara->reserved2=0;
}

----
Please help me
----
Thank you for posting your code. What error(s) are you getting?

----
This strikes me as the sort of thing that would have much more success on carbon-dev or the CarbonDev wiki or some other Carbon-oriented forum. Feel free to keep trying here, but it's really a Carbon question and I am personally clueless about it.

----

Be advised that Apple deprecated QuickDraw in Tiger. If you can avoid using it in new projects, you should do so.

----
You probably don't want to name your method "GetImageHandle" : 
Method names should start with a lower case letter.
"Get" has a reserved meaning different for the meaning implied in your code.

<http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html>

<http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html>

<http://www.stuffonfire.com/2007/04/29/get-stuffed/>

<http://chanson.livejournal.com/170309.html>

