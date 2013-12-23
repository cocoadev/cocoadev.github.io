---
layout: page
title: CocoaFob
---

CocoaFob is a set of helper code snippets for registration code generation and verification in Objective-C applications, integrated with registration code generation in Potion Store <http://www.potionfactory.com/potionstore>.

The current implementation uses DSA to generate registration keys, which significantly reduces chances of crackers producing key generators for your software. Unfortunately, it also means the registration code can be quite long and has variable length.

To make registration codes human-readable, CocoaFob encodes them using a slightly modified base32 to avoid ambiguous characters. It also groups codes in sets of five characters separated by dashes. A sample registration code produced using a 512-bit DSA key looks like this:

GAWQE-FCUGU-7Z5JE-WEVRA-PSGEQ-Y25KX-9ZJQQ-GJTQC-CUAJL-ATBR9-WV887-8KAJM-QK7DT-EZHXJ-CR99C-A

One of the advantages of DSA is that for a given registration name, each generated code is different, as there is a random element introduced during the process.

FEATURES

CocoaFob, a random collection of code snippets as it is, provides the following for your application:

- Secure asymmetric cryptography-based registration key generation and
  verification using DSA.

- Support for key generation in Objective-C and Ruby and verification in
  Objective-C for integration in both your Cocoa application and Potion Store.

- Support for custom URL scheme for automatic application registration.

There is no framework or a library to link against. You include the files you need in your application project directly and are free to modify the code in any way you need.

You may also find other snippets of code useful, such as base32 and base64 encoding/decoding functions, as well as categories extending NSString and NSData classes with base32 and base64 methods.

USAGE

The best way to get the latest version of the code is to clone the main Git repository:

git://github.com/glebd/cocoafob.git

You can also download the latest version from the CocoaFob home page at:
http://github.com/glebd/cocoafob/

