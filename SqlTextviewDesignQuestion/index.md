---
layout: page
title: SqlTextviewDesignQuestion
---

I am working on a textview that links to SQLite. The behavior I would like is as follows:

- sqlTextview is either empty (new) or has past statements that are uneditable
- User types in SQL statement
- New text is appended to NSString * queryString
- If last character = ";" then perform query
- queryString is appended to history text (NSMutableArray * history)
- Array is put into textview as blue and uneditable for visible history
- queryString is emptied and ready for next SQL statement
- User can recall past statements that are put into sqlTextview using up/down arrow keys that scroll through history array

The only way I can think of doing this is to use the keyDown event and append to both queryString and sqlTextview watching for the ";" key. When the ";" key is pressed, then execute query, append queryString to history array, and fill the sqlTextview with the history array strings using -componentsJoinedByString. keyDown also watches for up and down arrow keys and the string at the index is assigned to queryString.

This seems rather messy keeping track in both sqlTextview and queryString. Also, how do I make the history text (only) uneditable in sqlTextview while still allowing users to type in text below it? Can anybody help me work this out? Thanks for any help.

----

It sounds like you are after some form of terminal emulation. I too am interested in this type of functionality out of an NSTextView. I wonder if Apple will ever make the text view in Terminal app part of Cocoa. You really should try to find a way to emulate a terminal in an NSTextView first before trying to reinvent the wheel. Please keep us up to date on your progress.

BTW - you might want to check out this page http://iterm.sourceforge.net/

