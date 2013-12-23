---
layout: page
title: AppleDocumentationSearch
---

Today I got fed up with apples documentation search, more precisely I got fed up with WebObjects items always coming up first (try NSMutableArray, the Cocoa classes come up 7th!) so I wrote myself a nice bookmarklet. In fact if you place this as the first item in your bookmark bar you can access it with cmd 1!

    javascript:q%20=%20prompt(%22Enter%20a%20search%20phrase:%22,%20%22%22);%20if(q)%20{&#37;20location%20=%20%22http://developer.apple.com/cgi-bin/search.pl?&q=%22+q+%22&as_q=filetype:html%20OR%20filetype:htm&num=20&lr=lang_en&ie=utf8&oe=utf8&site=(cocoa)|(carbon)|(corefoundation)|(accessibility)%22;}%20else%20{&#37;20}

Enjoy.

*Nice! I know it's not exactly necessary most of the time, but it's convenient enough to keep. Thanks! --JediKnil*

I personally always had much better results simply searching for "search string site:apple.com" on Google than searching on Apple's search.

*WebObjects stuff still seems to rank higher in Google's database ...*

Great work! God initiative and makes it real easy (I, for example, sometimes suffer a little slow connection overseas, so this really speeds it up some). Cheers! -AntonKiland

