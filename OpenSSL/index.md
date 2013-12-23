---
layout: page
title: OpenSSL
---

OpenSSL is a library for doing Secure Sockets Layer communications. It also supports Transport Layer Security, and includes a fully-featured set of cryptography functions. Among other things, OpenSSL can do AES, RSA, MD5, SHA-1, and much more. It also includes a command-line tool capable of these things, called oddly enough     openssl.

The OpenSSL web site is located at http://www.openssl.org/

OpenSSL has been included with Mac OS X since at least 10.2, and probably much earlier. This makes it a great choice when you need crypto in your Cocoa applications. It provides a plain C interface, but it's easy to work with in Cocoa.

The bundled version of OpenSSL has been updated over the life of Mac OS X. Not all of these versions are compatible, so be sure that you use the correct SDK if you are targeting earlier versions of OS X.

Note that when using OpenSSL in your application, there are two different libraries that need to be included. The SSL end of things is included in     libssl, and the cryptography is in     libcrypto. You must link against the right one for what you're doing. Linking against     libssl exclusively will *not* work if you are using the crypto functions. Both these libraries are found in the standard location: /usr/lib/.

