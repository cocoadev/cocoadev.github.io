---
layout: page
title: NSStreamSOCKSProxy
---

I'm trying to use NSStream's SOCKS Proxy support, and I cannot seem to get it to work.  I've written a class to wrap around the NSStream (for purposes of UnitTesting) and these are the two relevant methods:

    
- (BOOL) setProperty:(id)value forKey:(NSString *)key
{
  BOOL success;
  NSLog(@"Value before set: %@ forKey: %@", value, key);
  success = [stream setProperty:value forKey:key];
  NSLog(@"Value after set: %@ forKey: %@",
        [stream propertyForKey:key], key);
  return success;
}

- (id) propertyForKey:(NSString *)key
{
  return [stream propertyForKey:key];
}


Elsewhere I call this code:

    
- (BOOL) enableProxyWithHostname:(NSString *)hostname
                          onPort:(int)port
                         version:(int)version
                        username:(NSString *)username
                        password:(NSString *)password
{
  BOOL success;
  NSArray *objects = [NSArray arrayWithObjects:
    hostname,
    [NSNumber numberWithInt:port],
    (version == 4 ? NSStreamSOCKSProxyVersion4 : NSStreamSOCKSProxyVersion5),
    username,
    password, nil];
  
  NSArray *keys = [NSArray arrayWithObjects:
    NSStreamSOCKSProxyHostKey,
    NSStreamSOCKSProxyPortKey,
    NSStreamSOCKSProxyVersionKey,
    NSStreamSOCKSProxyUserKey,
    NSStreamSOCKSProxyPasswordKey, nil];
  
  NSDictionary *proxyDictionary = [NSDictionary dictionaryWithObjects:objects
                                                              forKeys:keys];
  
  NSLog(@"Dictionary %@", proxyDictionary);
  
  success = [inputStream setProperty:proxyDictionary
                              forKey:NSStreamSOCKSProxyConfigurationKey];  
  if (success)
  {
    success = [outputStream setProperty:proxyDictionary
                                 forKey:NSStreamSOCKSProxyConfigurationKey];
  }
  
  return success;
}


When I call that third method it returns success!  But, if I then check the stream, the property is nil.  Any ideas?

