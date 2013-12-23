---
layout: page
title: OpeningUrlFromJava
---

The Java appkit does not implement NSURL, although NSWorkspace does implement the OpenURL method. OpenURL takes a java.net.URL instead of an NSURL. Therefore you can cause the system to open an URL using code like this:

    
try
{
  NSWorkspace ws = NSWorkspace.sharedWorkspace();
  java.net.URL url = new java.net.URL( "http://www.wrq.com" );
  ws.openURL( url );
}
catch( java.net.MalformedURLException e )
{
  e.printStackTrace();
}


StevePoole

