---
layout: page
title: InfoDotPlistAndNSPDFPboardType
---



I have a program that exports PDF, TIFF, and PNG files so I followed Apple's Sketch example when declaring types in my     Info.plist file:

    
<dict>
     <key>CFBundleTypeExtensions</key>
     <array>
          <string>foo</string>
     </array>
     <key>CFBundleTypeName</key>
     <string>MyDocumentFileType</string>
     <key>CFBundleTypeRole</key>
     <string>Editor</string>
     <key>NSDocumentClass</key>
     <string>MyDocument</string>
     <key>NSExportableAs</key>
     <array>
          <string>NSPDFPboardType</string>
          <string>NSTIFFPboardType</string>
          <string>MyPNGFileType</string>
     </array>
</dict>
<dict>
     <key>CFBundleTypeExtensions</key>
     <array>
          <string>pdf</string>
     </array>
     <key>CFBundleTypeName</key>
     <string>NSPDFPboardType</string>
     <key>CFBundleTypeRole</key>
     <string>None</string>
</dict>
<dict>
     <key>CFBundleTypeExtensions</key>
     <array>
          <string>tiff</string>
          <string>tif</string>
     </array>
     <key>CFBundleTypeName</key>
     <string>NSTIFFPboardType</string>
     <key>CFBundleTypeRole</key>
     <string>None</string>
</dict>
<dict>
     <key>CFBundleTypeExtensions</key>
     <array>
          <string>png</string>
     </array>
     <key>CFBundleTypeName</key>
     <string>MyPNGFileType</string>
     <key>CFBundleTypeRole</key>
     <string>None</string>
</dict>


Unfortunately, this has an unexpected consequence.  Calls to     -[NSDocumentController fileExtensionsFromType:] with     @"NSPDFPboardType" or     @"NSTIFFPboardType" return     nil but calling the method with     @"MyPNGFileType" still works as expected.

So why is this happening?  Thanks!

