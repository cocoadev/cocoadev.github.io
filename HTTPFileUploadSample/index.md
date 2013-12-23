---
layout: page
title: HTTPFileUploadSample
---

This sample code shows how to upload a file from a Cocoa application using HTTP POST. This code is meant to make it as easy as possible to upload files to your own webserver. To use this code you will need to do these things:

* Add the Uploader class to your application.
* Enable PHP on your server.
* Write a small PHP program to receive your files.

Each step will be described in detail below.  --SaileshAgrawal

----

**Step 1. The Uploader Class**

Add the code below to your application. The uploader class compresses the file before uploading it so you will have to add libz.dylib to your project.  You can find libz.dylib in /usr/lib.

    
@interface Uploader : NSObject
{
   NSURL *serverURL;
   NSString *filePath;
   id delegate;
   SEL doneSelector;
   SEL errorSelector;

   BOOL uploadDidSucceed;
}

- (id)initWithURL: (NSURL *)serverURL
         filePath: (NSString *)filePath
         delegate: (id)delegate
     doneSelector: (SEL)doneSelector
    errorSelector: (SEL)errorSelector;

- (NSString *)filePath;

@end
 

    
#import "Uploader.h"
#import "zlib.h"

static NSString * const BOUNDRY = @"0xKhTmLbOuNdArY";
static NSString * const FORM_FLE_INPUT = @"uploaded";

#define ASSERT(x) NSAsert(x, @"")
#define Log(x,y)

@interface Uploader (Private)

- (void)upload;
- (NSURLRequest *)postRequestWithURL: (NSURL *)url
                             boundry: (NSString *)boundry
                                data: (NSData *)data;
- (NSData *)compress: (NSData *)data;
- (void)uploadSucceeded: (BOOL)success;
- (void)connectionDidFinishLoading:(NSURLConnection *)connection;

@end


@implementation Uploader


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader initWithURL:filePath:delegate:doneSelector:errorSelector:] --
 *
 *      Initializer. Kicks off the upload. Note that upload will happen on a
 *      separate thread.
 *
 * Results:
 *      An instance of Uploader.
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (id)initWithURL: (NSURL *)aServerURL   // IN
         filePath: (NSString *)aFilePath // IN
         delegate: (id)aDelegate         // IN
     doneSelector: (SEL)aDoneSelector    // IN
    errorSelector: (SEL)anErrorSelector  // IN
{
   if ((self = [super init])) {
      ASSERT(aServerURL);
      ASSERT(aFilePath);
      ASSERT(aDelegate);
      ASSERT(aDoneSelector);
      ASSERT(anErrorSelector);

      serverURL = [aServerURL retain];
      filePath = [aFilePath retain];
      delegate = [aDelegate retain];
      doneSelector = aDoneSelector;
      errorSelector = anErrorSelector;

      [self upload];
   }
   return self;
}


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader dealloc] --
 *
 *      Destructor.
 *
 * Results:
 *      None
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (void)dealloc
{
   [serverURL release];
   serverURL = nil;
   [filePath release];
   filePath = nil;
   [delegate release];
   delegate = nil;
   doneSelector = NULL;
   errorSelector = NULL;

   [super dealloc];
}


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader filePath] --
 *
 *      Gets the path of the file this object is uploading.
 *
 * Results:
 *      Path to the upload file.
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (NSString *)filePath
{
   return filePath;
}


@end // Uploader


@implementation Uploader (Private)


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader(Private) upload] --
 *
 *      Uploads the given file. The file is compressed before beign uploaded.
 *      The data is uploaded using an HTTP POST command.
 *
 * Results:
 *      None
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (void)upload
{
   NSData *data = [NSData dataWithContentsOfFile:filePath];
   ASSERT(data);
   if (!data) {
      [self uploadSucceeded:NO];
      return;
   }
   if ([data length] == 0) {
      // There's no data, treat this the same as no file.
      [self uploadSucceeded:YES];
      return;
   }

   NSData *compressedData = [self compress:data];
   ASSERT(compressedData && [compressedData length] != 0);
   if (!compressedData || [compressedData length] == 0) {
      [self uploadSucceeded:NO];
      return;
   }

   NSURLRequest *urlRequest = [self postRequestWithURL:serverURL
                                               boundry:BOUNDRY
                                                  data:compressedData];
   if (!urlRequest) {
      [self uploadSucceeded:NO];
      return;
   }

   NSURLConnection * connection =
      [[NSURLConnection alloc] initWithRequest:urlRequest delegate:self];
   if (!connection) {
      [self uploadSucceeded:NO];
   }

   // Now wait for the URL connection to call us back.
}


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader(Private) postRequestWithURL:boundry:data:] --
 *
 *      Creates a HTML POST request.
 *
 * Results:
 *      The HTML POST request.
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (NSURLRequest *)postRequestWithURL: (NSURL *)url        // IN
                             boundry: (NSString *)boundry // IN
                                data: (NSData *)data      // IN
{
   // from http://www.cocoadev.com/index.pl?HTTPFileUpload
   NSMutableURLRequest *urlRequest =
      [NSMutableURLRequest requestWithURL:url];
   [urlRequest setHTTPMethod:@"POST"];
   [urlRequest setValue:
      [NSString stringWithFormat:@"multipart/form-data; boundary=%@", boundry]
      forHTTPHeaderField:@"Content-Type"];

   NSMutableData *postData =
      [NSMutableData dataWithCapacity:[data length] + 512];
   [postData appendData:
      [[NSString stringWithFormat:@"--%@\r\n", boundry] dataUsingEncoding:NSUTF8StringEncoding]];
   [postData appendData:
      [[NSString stringWithFormat:
        @"Content-Disposition: form-data; name=\"%@\"; filename=\"test.bin\"\r\n\r\n", FORM_FLE_INPUT]
       dataUsingEncoding:NSUTF8StringEncoding]];
   [postData appendData:data];
   [postData appendData:
      [[NSString stringWithFormat:@"\r\n--%@--\r\n", boundry] dataUsingEncoding:NSUTF8StringEncoding]];

   [urlRequest setHTTPBody:postData];
   return urlRequest;
}

/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader(Private) compress:] --
 *
 *      Uses zlib to compress the given data.
 *
 * Results:
 *      The compressed data as a NSData object.
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (NSData *)compress: (NSData *)data // IN
{
   if (!data || [data length] == 0)
      return nil;

   // zlib compress doc says destSize must be 1% + 12 bytes greater than source.
   uLong destSize = [data length] * 1.001 + 12;
   NSMutableData *destData = [NSMutableData dataWithLength:destSize];

   int error = compress([destData mutableBytes],
                        &destSize,
                        [data bytes],
                        [data length]);
   if (error != Z_OK) {
      LOG(0, ("%s: self:0x%p, zlib error on compress:%d\n",
              __func__, self, error));
      return nil;
   }

   [destData setLength:destSize];
   return destData;
}


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader(Private) uploadSucceeded:] --
 *
 *      Used to notify the delegate that the upload did or did not succeed.
 *
 * Results:
 *      None
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (void)uploadSucceeded: (BOOL)success // IN
{
   [delegate performSelector:success ? doneSelector : errorSelector
                  withObject:self];
}


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader(Private) connectionDidFinishLoading:] --
 *
 *      Called when the upload is complete. We judge the success of the upload
 *      based on the reply we get from the server.
 *
 * Results:
 *      None
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (void)connectionDidFinishLoading:(NSURLConnection *)connection // IN
{
   LOG(6, ("%s: self:0x%p\n", __func__, self));
   [connection release];
   [self uploadSucceeded:uploadDidSucceed];
}


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader(Private) connection:didFailWithError:] --
 *
 *      Called when the upload failed (probably due to a lack of network
 *      connection).
 *
 * Results:
 *      None
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (void)connection:(NSURLConnection *)connection // IN
  didFailWithError:(NSError *)error              // IN
{
   LOG(1, ("%s: self:0x%p, connection error:%s\n",
           __func__, self, error description] UTF8String]));
   [connection release];
   [self uploadSucceeded:NO];
}


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader(Private) connection:didReceiveResponse:] --
 *
 *      Called as we get responses from the server.
 *
 * Results:
 *      None
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

-(void)       connection:([[NSURLConnection *)connection // IN
      didReceiveResponse:(NSURLResponse *)response     // IN
{
   LOG(6, ("%s: self:0x%p\n", __func__, self));
}


/*
 *-----------------------------------------------------------------------------
 *
 * -[Uploader(Private) connection:didReceiveData:] --
 *
 *      Called when we have data from the server. We expect the server to reply
 *      with a "YES" if the upload succeeded or "NO" if it did not.
 *
 * Results:
 *      None
 *
 * Side effects:
 *      None
 *
 *-----------------------------------------------------------------------------
 */

- (void)connection:(NSURLConnection *)connection // IN
    didReceiveData:(NSData *)data                // IN
{
   LOG(10, ("%s: self:0x%p\n", __func__, self));

   NSString *reply = [[[NSString alloc] initWithData:data
                                            encoding:NSUTF8StringEncoding]
                      autorelease];
   LOG(10, ("%s: data: %s\n", __func__, [reply UTF8String]));

   if ([reply hasPrefix:@"YES"]) {
      uploadDidSucceed = YES;
   }
}


@end
 

You can now upload a file using the following syntax:
    
      Uploader alloc] initWithURL:[NSURL [[URLWithString:@"http://my-server.com/uploader.php"
                              filePath:@"/Users/someone/foo.jpg"
                              delegate:self
                          doneSelector:@selector(onUploadDone:)
                         errorSelector:@selector(onUploadError:)];


----

**Step 2. Enable PHP**

You need to enable PHP on your webserver. For testing purposes this means your local Macintosh.

If you're on Tiger edit /etc/httpd/httpd.conf. On Leopard edit /etc/apache2/httpd.conf. For example:
    
cd /etc/httpd
sudo pico httpd.conf


Search for LoadModule php and remove the number sign (#) at the beginning of the line. Next, for Tiger only, search for AddModule php and remove the number sign at the beginning of the line too.

Start your http server by going to System Preferences and enabling Web Sharing in the Sharing pane.

----

**Step 3. Write a PHP web application**

The above code uses a HTML form to submit the file. This means that the fields of the form are specific to this web app. If you have a pre-existing web app then search for FORM_FLE_INPUT in the uploader code and change it to match your website.

The following PHP code receives the name of the file in the "uploaded" parameter. If uploading was successful then the file is copied into the upload directory. Save this code as uploader.php and put it on your webserver.

    
<?php
$target = "upload/";
$target = $target . basename( $_FILES['uploaded']['name']) ;
$ok=1;
if(move_uploaded_file($_FILES['uploaded']['tmp_name'], $target))
{
   echo "YES";
}
else {
   echo "NO";
}
?> 


If you're having trouble getting your web application to work it may help to test it using a web browser. You can create a web page to upload a file using the following code. Same the code as test.php and put it on your webserver.

    
<form enctype="multipart/form-data" action="uploader.php" method="POST">
<input type="hidden" name="MAX_FILE_SIZE" value="100000" />
Choose a file to upload: <input name="uploaded" type="file" /><br />
<input type="submit" value="Upload File" />
</form>


----

At this point you should be able to upload arbitrary data to your webserer. Note that since the above code compresses the data before transmitting it you will need a small program to decompress it on the server. If compression is not necessary than simple remove the call to the compress method in the above code.

----

I just used your sample to great success, thankyou very much. However I needed to make a few tweaks to make it compile. The ASSET and LOG functions weren't working, so I commented them all out, and the #define markings you made for them. I'm not sure why they weren't but the code would compile and run fine afterwards. You might also want to add a bool flag to the UELUploader initWithURL: method to signal for compression - just for convenience. It is also authentication compatible, just add the name:password to the URL.

    
Uploader alloc] initWithURL:[NSURL [[URLWithString:@"http://name:password@my-server.com/uploader.php" ...


----

I have been trying to get this to work but am having trouble. Like the previous commenter I had to comment out the ASSET and LOG. I am getting the following response from the server:

 <br />
<b>Warning</b>:  move_uploaded_file(upload/test.bin) [<a href='function.move-uploaded-file'>function.move-uploaded-file</a>]: failed to open stream: No such file or directory in <b>subdomain.myserver.com/uploader.php</b> on line <b>5</b><br />
<br />
<b>Warning</b>:  move_uploaded_file() [<a href='function.move-uploaded-file'>function.move-uploaded-file</a>]: Unable to move '/tmp/phppw6XMF' to 'upload/test.bin' in <b>subdomain.myserver.com/uploader.php</b> on line <b>5</b><br />

Also when I run the web browser one I get the same thing:

Warning: move_uploaded_file(upload/testfile.txt) [function.move-uploaded-file]: failed to open stream: No such file or directory in subdomain.myserver.com/uploader.php on line 5

Warning: move_uploaded_file() [function.move-uploaded-file]: Unable to move '/tmp/php7UoHOZ' to 'upload/testfile.txt' in subdomain.myserver/uploader.php on line 5
NO

All I did is copy and past the code. Would love to have some help.

----

I figured it out!
the "upload" folder was spelled "uploads"
Stupid mistake but maybe my waisted time will help someone else save some time.
Pour vous joindre   maintenir votre  numéro, vous aurez  peuvent avoir  comptes   propriétaire  (code RIO ) [http://obtenir-rio.info rio bouygues]. Vous obtiendrez  est certain d'obtenir gratuitement pour  aucun coût par appelant   mots  du serveur ou du service à la clientèle  clientèle   du   entreprise [http://obtenir-rio.info/rio-bouygues rio bouygues] . Vous ne  CAN   acquérir  un SMS  avec vos . Avec  du  [http://obtenir-rio.info/rio-orange rio orange], alors  vous êtes capable d'  sur le  offre de  de son  ON  orange orange .

