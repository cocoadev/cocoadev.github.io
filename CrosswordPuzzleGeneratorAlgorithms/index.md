---
layout: page
title: CrosswordPuzzleGeneratorAlgorithms
---

I am playing around with the idea of making a crossword puzzle generator.  I have two NSMutableArrays.  One is filled with NSString clues and the other is filled with NSString answers.  Does anyone know any good algorithms for weaving the answers together to form a crossword puzzle?  Thanks in advance for any leads.  --AlexanderD

----

Why not an NSMutableDictionary?  Then you can make the key to the clues the answers.  Or, if you don't want to do that, you can make the numbers correspond and use the same variable for each of them.

--CharlieMiller

Charlie, you are right.  Using an NSMutableDictionary would make sense.  I actually have a separate variable that keeps track of the current index number in each array so they are synchronised.  That part of the program is working, though.  Clunky, but working.  When I get the rest worked out, I will work on converting my two NSMutableArrays into one NSMutableDictionary, like you suggest. --AlexanderD

----

You need to: 
* Set up a 2x2 grid (to store letters)
* Decide on what squares will be black and which white
* Try to fill an answer into every line
* Remember which answer went where, which fit but you didn't try, and which fit but you've already tried
* If it becomes impossible to fill a line, you need to backtrack and try different answers elsewhere


I suggest you pick a grid, number all the lines in standard crossword style (8 across, 2 down, etc), decide on some order for these, then do the above, trying to fill each line in the order you decided. Make sure you never repeat a combination (waste of time) but have the potential to try them all before giving up.

Finally, this will take a long time.
----
Update

I figured out how to take two strings and determine all the ways the two can cross each other, in the way crossword puzzles do.  I search for all the ways the two can cross and then give each crossing a score.   A crossing gets more points the further in each word it crosses.  Then I sort the different crossings by score and pick the one that has the highest score.  Then I draw the two strings, putting the letters in cells of an NSMatrix.  It took me a while to do this, but now my program can make a darn good two word crossword puzzle.

I am mentally stumped as to how to add more strings.  With over three words, you will have to consider a single word overlapping with two other words.  You will have to eliminate those words that might hit other words in the wrong way.  Any ideas for how to make three or more word crossword puzzles? --AlexanderD

----

What steps would you go though to do this by hand?  I have never made a good X-word before, so I can't imagine how.  If you can come up with a set of steps to do it by hand, you can start writing an algorhithm.  Other then that, the only other way I could think of would be brute-force.  Take the first word (maybe longest), and lay it out, then try to connect it with the second word, then add on the third word, etc... until you have added all the wordsl.  If a word does not fit anywhere in the puzzle, put it into a second pile, and try it later.  After you have added all the words, assign a score to the result (maybe 1/[height*width], that way, smaller puzzles would get higher scores.  After that, change something and try again and compare the next result's score to the first one, repeat until the cows come home. --DerekCramer

