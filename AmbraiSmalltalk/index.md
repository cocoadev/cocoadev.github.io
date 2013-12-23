---
layout: page
title: AmbraiSmalltalk
---




I just discovered an interesting site at [http://ambrai.com] where they've brought the Smalltalk programming environment to OS X in a more elegant way than I've seen before.  Sorry this is a bit off-Cocoa, but seems to me to be relevant for those interested in Cocoa's roots.  Would like to know your thoughts.

The Currency Tutorial (45 lines) from their tutorials page [http://ambrai.com/smalltalk/tutorials/tutorial1/index.html] looks and
behaves just like a native application.  Unfortunately this is the only tutorial on their site :(

    
exchangeRate := 1.33.

(window := MacWindowFrame new)
  text: 'Currency Converter';
  position: 20@60;
  extent: 300@160.

(amountLabel := MacTextLabel new)
  text: 'Amount to convert:';
  extent: 100@15.

(amountField := MacTextField new)
  extent: 100@15.

(resultLabel := MacTextLabel new)
  text: 'Result:';
  extent: 100@15.

(resultField := MacTextField new)
  disable;
  extent: 100@15.

(convertButton := MacPushButton new)
  text: 'Convert';
  beDefault;
  extent: 100@20;
  when: #clicked evaluate: [
    resultField text: (
      amountField text asNumber * exchangeRate
    ) asString].

window
  layout: LinearLayout new beVertical;
  addControl: amountLabel;
  addControl: amountField;
  addControl: resultLabel;
  addControl: resultField;
  addControl: convertButton layoutConstraints: (
    LinearLayoutConstraints new
      anchorRight;
      stretchHorizontally: false;
      yourself).

window open


----

I just downloaded the software and ran though the tutorial. This is my first experience with Smalltalk, so it is difficult to compare with any other environment.

First of all the language seems very nice although I don't fully understand it yet. I've always wanted to learn Smalltalk, and this looks like a good way to start. The tutorial example did not take very much code considering it was building the GUI and handling the calculation, but I can't imagine building a user interface intensive application through this technique. It would be extremely cool if they could integrate Interface Builder, but that may be near impossible.

I found the IDE lacking in many areas. Most of all, where are the preferences? There appears to be no way to even change the font. It would help a lot if they added syntax coloring, line numbers and automated indentation as well. Perhaps these things aren't as necessary for Smalltalk as they are in other languages, but they would still be nice.

Over all, the IDE seems like a good first beta. There appears to be a lot under the hood and they may just need to beef up the UI with some more features. I look forward to playing around with Smalltalk in the future and seeing how this tool grows.

-- RyanBates

*BTW, it is possible to change the font by copying and pasting rich text from TextEdit, however, it doesn't appear to keep the attributes when saving so it's not all that useful.*

----

if you want to learn more about Smalltalk take a look at  http://www.smalltalk.org  and  http://www.whysmalltalk.com.  either of these will lead you to other resources.

-- Cucumber

