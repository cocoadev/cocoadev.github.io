---
layout: page
title: UnitTestingQuestions
---

How do we test code which use floating points? E.g. we may have a function which return the intersection(s) between two lines. But floating points can only be compared within a given epsilon, and what good is our test if it is not exact?

*
 Answer: Floating point calculations can only be guaranteed to be accurate within a certain epsilon. The degree of accuracy required should be an understood and agreed upon specification of the calculation. Your test can **exactly** verify that the result of the calculation returns **exactly** the right value - to the right precision. One simplistic way of doing this is using sprintf with a format specification to translate the floating point number to a string up to the required specification, and comparing those strings. Given your own requirements, there may be better methods of comparing accuracy.

The result of that test is exact, though. - General/TimHart

Agreed - what good is the code if it isn't exact?  One of the points of unit testing is that it tests with respect to the rest of the code, not to some arbitrary standard.  If the code can only compare to within epsilon, an exact test isn't of much value to anyone. - General/MattSmillie

*
----

And how do we actually calculate the expected result before we know the function is correct? If we must use the actual function to get the expected result, then surely we have already ensured the function works (but w/o using unit tests).

*
Answer: In order to succesfully implement any algorithm/calculation, you must have **some** way of validating it. At worst, complicated calculations require manual verification. If this is the case, pick a set of inputs and manually calculate the result. Now write the test using those numbers, and implement the calculation to satisfy the test.

The practice of testing is partially an art form. Good starting points are boundary conditions - both for input and return values. If the calculation is complicated, it might be useful to break it into smaller pieces, and write tests for each piece. - General/TimHart
*
----

How can we test code with heuristic solutions? I.e. the result may not be the same each time. Should we then write a program which verify the result based on some properties of the solution?

*
Answer: While the overall functionality of the system may allow different results for the same inputs, there is some 'state' of the system somewhere that drives those changes. Testing of any system involves putting the system into a known, predefined state beforehand. In simple cases, that means simply calling the method/function with the same parameters. In more complicated scenarios, the developer must set up the database with a set of  known records, copy a file designed specifically for the test to a certain location, or setting up a 'stub' external system that will always behave in a certain way needed for the test - General/TimHart
*
----

How can we test code returning or receiving advanced data structures? E.g. we may have a function which return the dependency graph of source files. How do we easily construct the expected result for more than 2-3 files?

*
Answer: Set up the initial state so that the returned dependency graph isn't complicated. Test boundary conditions, not unneccesarily complicated conditions unless they are required to exercise a peice of code. - General/TimHart
*
---- 

In non-transaction based system, is there anything pointing toward that errors should arise in the primitive functions (which can be unit tested)? The problems I encounter are mainly memory related (i.e. working on released memory) or some very complex interplay of things, which either can't possibly be tested with unit tests, or which I didn't imagine at the time of designing the function (and unit tests are likewise limited by my imagination).

*
Answer: I'm attempting to write some framework tools to help those with memory leaks. To date, I have no way of unit testing early releases of memory. In OS X, General/CrashCatcher does a great job of logging the stack trace when I SEGV, so it's never really been necessary. I do concede that memory related issues are difficult, and cannot be tested cleanly. At a minimum, though, you can certainly write a test that will crash your method if it doesn't properly use memory. If your test crashes, look up the stack trace in General/CrashCatchers log file, and continue from there.

As stated before, testing is an art form, and does require imagination. You definitely get better with practice and experience. One practice that helps is once you discover a bug, immediately write a unit test that aggravates that bug. Doing so helps you not only fix that problem, but increase your test writing skills for the future. - General/TimHart
*

----
Isn't *testing is an art form* against the principles of unit testing? I thought that unit testing was a generic tool applicable to all problems, i.e. the hammer for all kinds of nails. I often have discussions with proponents of unit testing, I have done my own type of testing for the last 10+ years, and I write very reliable code, but I certainly do not do unit testing, because I do not feel they add anything to my existing suite of testing tools, and I am much more comfortable with a function which I have proven correct (or at least convinced myself of its correctness), than with a function which return the proper results for a test set designed by the implementor of the function, because bugs in my experience are much more devious than so -- they exist because the designer of a function did not consider certain degenerate cases, and if he didn't do that while designing the function, why should he do so whe designing the test?


As implied elsewhere in this thread, bugs are also mainly about memory management. Returning references to stack allocated memory which was from a temporary which is out of scope at the time of the return etc. You really need to understand every line of code, not just the General/APIs.

*
<Insert Tim "The Tool Man" Taylor HWEUUH? grunt here>

Unit testing is a tool. No tool solves all problems. I have no doubt you write reliable code. As do I. Aren't you tired of manually retesting that code every single time you touch something that method/function depends on? Don't forget the **reproducibility** factor of unit tests. Sure you can test everything the computer does, but the computer can retest much more quickly, and faithfully, than you can - or should want to. These arguments are analogous to "Why would I want a program to check the syntax of my code? I can check my syntax just fine without it, thank you very much". Of course you can. I can to. Do I really want to waste precious productivity time to something that the computer can do much faster? I'd rather think about the problems that the computer CAN'T solve for me. Besides, most unit testing proponents would argue that it's not the responsibility of the designer to write the test, but of the implementor. Degenerate cases are situations that I consider first when writing unit tests. Mentally I group them in with 'boundary conditions' - General/TimHart
*

----

Also, is unit tests to ensure that the function is correct? or is it merely a way to put "guards" on the function, so that if anyone in the future should change it, and introduce a bug, the unit tests may detect it? I think this is important to establish, because unit tests alone cannot verify that a mildly complex function is correct, take quick sort, there are pretty much unlimited number of inputs, and bugs may not even affect the output, but only affect the performance, and they may only show up for a fraction of a percent of the possible inputs.

*
Answer: To ensure that the function is correct. It either is or isn't, there is no other answer. Even in your heuristics problems, the heuristics algorithm is driven by state somewhere. Given a heuristics state and a problem, the answer will always be the same. - General/TimHart

Here I disagree - unit tests are not a proof of correctness, merely some indication of predicted behaviour.  This is more than an academic distinction, since it should influence your outlook on what constitutes an appropriate test, and you should remain aware of it.  Take the example of quicksort: I don't have to prove that it works in general, merely that my implementation works.  To do that, I require one general test: the output is in ascending order.  I apply this test over a few inputs, say, a random one, one with one entry in the list, and one (this is key) which I specifically design to break my own code.

As for performance or very stringent proofs of correctness, don't fit a square peg to a round hole; use a profiler, or some formal analysis, whatever works.  Unit tests aren't particularly suitable to finding performance problems, I've found. - General/MattSmillie
*

A problem often arising when implementing quick sort (or binary search) is, that the pivot element is not removed from the set when calling down. This rarely cause a problem, because a new pivot element is picked each time, and the set is (hopefully) halved, and thus will eventually get down to less than 2 elements (and the recursion will terminate). So even extensive unit testing may not show this problem. So we need a better way, making me think of unit tests a little like the helmet that parachuters wear, surely it's handy if they bump their head, but the real security comes from another place.

*
The term 'rarely' should raise a flag. Boundary condition. Not necessarily a boundary in terms of input ranges, but certainly a unique case. Can it be tested for? Certainly, because sometimes it DOES cause a problem, and those situations( extreme cases, boundary cases, degenerate cases ) should be coded for in the test.
- General/TimHart
*

With respect to performance, I was again referring to a **broken** implementation. Many algorithms, when implemented wrong, will still be able to produce the correct solution, but in a much worse time complexity than when designed.

----

If unit tests are merely a form of "tamper detection", then why should I use it in my one-man projects? When I tamper with working functions, it is most likely to improve their output, so the test would break anyway (e.g. reflow a paragraph with equal margins may be done with different results).

*
Because the interaction of 10 to 20 thousand lines of code is complicated, and impossible for one man to keep track of. Even on a one man project. People are fallible and misremember preconditions, requirements, and behavior. We also don't handle abstraction very well. Abstraction hides details that can make the system behave differently than it should. Tests don't care about abstraction. They don't misremenber. They behave exactly the same way every time they are run. Why bother trying to handle this complicated situation manually? We created computers to do EXACTLY this kind of work. - General/TimHart

I agree completely.  Let's work through some examples: you're rewriting function A, and its output will either change, or not.  If you don't expect it to change, then the unit test is a good thing to have.  If you do expect the output to change, you may have to rewrite your unit tests (though potentially not, depending on how you've set up your test coverage), which is not, generally speaking, a big deal - the modifications are usually parallel to the code.  What may happen is that after you fix the test for A, you notice that B, D, and Q are now failing due to the changes.  This is where unit tests are extremely helpful in maintaining a relatively large project. - General/MattSmillie

*
----

It probably sounds as a clich�, but I really do not make bugs in the simple functions, which I think is the only part of my code which can be tested -- and since I easily write 5-10,000 lines of code in a month, and sometimes decide to throw out equal amounts of code, because I got a better idea for how to structure things, then I only see unit tests as a) a waste of time and b) a burden around the foot, because I restructure my code, change output of functions and similar which would require me to update unit tests.

*
My level of code production is similar. My propensity to throw code out is also on par with yours. Tests are as useful for finding runtime mistakes as the compiler is for finding compile-time mistakes.

I can also claim that I don't make mistakes in simple functions. I think most developers can say the same. But functions call other functions with call others. At certain points in the execution stack, external state can become a factor. The result is that even if your function/method is only 7 lines of code, the resulting work, and complexity thereof, can be enourmous. Why not let the computer does what it does best: mundane work really fast!

As for throwing out code to restructure, that is where tests really shine. Most of the time you have the same functionality implemented in a different metaphor/structure. You will most definitely have tests that test this same functionality that can quite easily be modified to call your new class/method. As you restructure, you probably already let the compiler catch all the places that you've 'broken' as a result of the metaphor/structure change. Why not let the tests catch all the places where you broke the functional/runtime behavior as well? - General/TimHart

As a closing comment, I'd encourage you to think about where and how you can apply unit tests; simply changing your opinion about what constitutes appropriate test coverage can vary the amount of tests you have to do by orders of magnitude.  Remember that you're not writing these tests blind; you can structure them to exercise your code.  For instance, you can write a set of tests that will test every conditional in a function, rather than every possible output.  You can take a co-worker's code, and write a test specifically to break it (and then insist he buy you lunch as a tribute).  Remember that unit testing is just another tool.  It's (in many people's experience) a very useful one, but if you need something else to make things work, use that as well. - General/MattSmillie

*

----

Most of the tutorials I've seen on General/UnitTesting do very tedious work to satisfy the tests and then slowly refactor and add more assertions until they come up with the correct solution - and usually the correct solution is obvious to begin with. If you are experienced in General/UnitTesting, do you still go through these tedious steps? Is this to ensure that your original theory was the best solution? (an example tutorial is http://www.differentpla.net/~roger/devel/xp/test_first/to_roman/ )

*

Well, "test first" and XP (what the tutorial covers) isn't unit testing, though you can use unit testing to work in a test-first manner.  Test-first is a way of using unit tests, unit tests are just a tool.  My general approach is top-down; design high-level interactions (e.g. documents->tokeniser->database), then a detailed UML-esque design for each of the major components.  I generally start writing unit tests at that point, because it's where you've started to define behaviour.  Most of the tests get written while doing the actual coding, as I see ways where bad input could break a particular method.  Where I end up working as in the tutorial is when the higher-level tests start to fail for one reason or another; these indicate my technique isn't working the way I thought it would, and generally needs some refactoring and adjustment.  On large projects, this is almost expected, which is why I tend to prototype a lot of things in Perl/Python/Ruby, and recode them later.

Also, you need to bear in mind that simple ideas presented in tutorials are meant to illustrate the technique involved without making you sort out the complexities of the idea itself.  A tutorial on unit testing using a Combinatoric Categorial Grammer-based parser isn't much good to anyone who doesn't know how those work to begin with.  So while the idea and its solution are generally simple and obvious, it's the method that's the important part, and that's what you should concentrate on. - General/MattSmillie

*

It really would be useful if there was a tutorial writing a *Combinatoric Categorial Grammer-based parser* -- in my own code, I rarely find more than 10% of code resembling that of the tutorials I have read, and although I have not kept a record, this is not where problems arise.

----

**The following was last during a page edit, but I have restored it:**

Big thanks to General/TimHart for providing the above answers! To clarify on two points:

With heuristic solutions I was referring to either problems that have several (legal) solutions, where we just want one of them or problems which are too hard for an exact solution, so we just want an approximation. The problem here (with unit tests) is that the solution may be achived through randomized programming or, we may make changes in the function (or some of the functions it makes use of) which make it give other (but still legal) solutions than before we made the changes.

*
In the case where your answer is any one of an expected set: Is this expected set part of the specification? In other words, is it a known set that you can code into your test case? If so, then the unit test can assert that the response is a member of this predefined set. 
*

It might be -- if we take the two examples from above, one being to reformat a paragraph so that it has equal right margin. The spaces can be distributed in many different ways, and we could make a set of all possible distributions and test if it is in any of these.

*
Your test should be much easier than that! The result of your function is 'equal right margin', not number of words per line, or an exact rendering of the paragraph. So have the test code call the method/function that reformats the paragraph. For each line of the paragraph, determine the right margin ( yes, you're probably reusing the same code that the reformating function uses to determine right margin ). Verify that all lines have the same right margin ( or a right margin within some range ).

Now if you think a bit more critically of this test, you might think - wait - while that is correct, I won't be happy with 2 words on each line - one at the beginning, and one at the end of each line. Great! Then in fact your specification is slightly more complicated than 'all lines have the same right margin'. What are your tolerances? Define them! Designing a unit test isn't easy, and at times may call for the business analyst/user to answer some tough questions. Those questions need answering, though. - General/TimHart

*

----
The more I thought about my above answer, the more I didn't like it. I think a better, and easier test would be something like this:

Don't do this in one test - do it in several. One test for each type of condition the algorithm will have to face. In other words, write a test for each of the following input paragraphs: ( I'm really just guessing at your requirements - so cut me a bit of slack... :P

nil/NULL
Acceptable Input? What should the behavior be for this?

'a'
Acceptable Input? What should the behavior be for this?

'a b' 
Does your algorithm want the 'a' left justified and the 'b' right justified?

'a b c'
Assume the 'c' must be right justified, what's the appropriate location/range of locations for 'b'?

'yabadabadabadabadabadaba-dabadabadaba'
Assume your algorithm will line break on a hyphen, and the entire text is too big for a line

'imagine_a_line_that_is_too_big_to_fit_on_a_line_by_itself_and_underscores_are_non_breaking_characters'
How is your algorithm supposed to handle this?

'What about a non-breaking space here--> <-- that is purposefully placed at a line break boundary?'

I'm sure there are several other possibilities you have to consider. For each of these cases, I would imagine that the result wouldn't change, regardless of changes to the algorithm. Each one tests a single property. If you decide that you must change an aspect of the algorithm, than you have a single test to rewrite.

This is what I mean by testing being partially an art form. Figuring out the right way to test a black box isn't always easy.
--General/TimHart

*Yes, there would be other tests for this to be useful -- the algorithm is very simple, it breaks the input into words, then accumulates the lengths plus one space, as soon as it gets above the line width, the words are copied to the output iterator, but with the space between words calculated as a fraction of the total number of spaces needed for this line, similar to:*
    
int boundaries = ...; // number of words - 1
int spaces = ...; // the number of spaces we want to output
for(int i = 0; it != last; ++it, ++i)
{
   out = std::copy(it->first, it->second, out);
   if(i != boundaries)
   {
      int a = (spaces *    i  + (boundaries >> 1)) / boundaries;
      int b = (spaces * (i+1) + (boundaries >> 1)) / boundaries;
      out = std::fill_n(out, b - a, ' ');
   }
}

*This is IMO the most advanced part of the algorithm, and is in essence a linear interpolation, which, if wrong, will distribute the spaces incorrect, even though it may still produce lines with the correct length.*

*Personally I find it much easier and satisfying to prove that this interpolation is correct, rather than construct a dozen test cases, although I naturally will run test data through my function.*

*P.S. the code above iterates std::pair of iterators arrising from breaking the text into words like this:*
    
template <typename _Iter, typename _OutputIter>
void break_into_words (_Iter first, _Iter last, _OutputIter out)
{
   _Iter bow = std::find_if(first, last, std::not1(std::ptr_fun(is_space)));
   while(bow != last)
   {
      _Iter eow = std::find_if(bow, last, is_space);
      *out++ = std::make_pair(bow, eow);
      bow = std::find_if(eow, last, std::not1(std::ptr_fun(is_space)));
   }
}

*P.P.S. would you recommend also doing unit tests of functions like the     break_into_words above?*

That PPS is a thorny question among those who do unit testing. It's really a question of granularity - how fine a grain is important to the project? I personally don't usually write test cases for simple getters & setters. Others in the unit testing community would scold me severely for even thinking that. They have a point - some of the bugs that make it into UAT on my projects are best demonstrated by writing a test case for one of those 'supporting' functions that I glossed over.

At times what comprises a 'unit' deserving a test is a judgment call. Experience fine tunes that judgment -- General/TimHart
----

For the case with the dependency graph for a set of source files, this graph should be known in advance, but from this graph we may wish to create a build script, and here we again have several solutions, depending on how we traverse the graph.

Actually, thinking about it, none of the above is really 'heuristic solutions', because the result is always optimal, the problem is only, that there are several optimal solutions.

A heuristic is when the result returned is not optimal -- for example if we take the quick sort, we may wish to use the median of the collection as pivot element, but that would force us to look at all the elements, so instead we randomly pick 5 elements, and calculate the median of these 5 elements, and use the result as pivot element (IOW we may get a different result for the same set of elements, neither being the real median).

*
Regarding the approximation - it's hard to give answers without details, but I appreciate you wanting to explore in generalities. If you have some kind of requirement for the calculation the method is to perform, than a unit test makes you answer the question "How exact must my answer be?" or "What are the legal boundaries of my approximation?" How close is close enough? You really should be able to answer that question when writing your test. Once you can answer that question, you should be able to come up with a test to verify your accuracy boundaries.-General/TimHart  
*

Regarding the "advanced datastructures", I was mostly referring to the task of creating these. I.e. it might be possible to create the expected graph by hand, but it's tedious and errorprone, and imagine something like a function which return the convex hull for a set of points. The way I normally test these sort of things (i.e. geometry related algorithms or graph/tree algorithms) is simply to draw it, and look at the result (using General/GraphViz when it comes to trees/graphs), that is also invaluable during the development of the algorithms � but of course, there is no automatic test for later use. 

*
 I agree - hand coding structures to compare to are tedious. Depending on the size of your result, you might consider one of several options: 


*Programatically capture to-file a result set that you're willing to accept as having passed your preconditions to a file. The test then recreates the compared result from the file. This choice suffers from the fact that you're probably using the function you just wrote to generate the results you're testing against, so it's important to critically consider your initial validity test for this function. 
*Test the result set for critical points. These may be boundaries or anything else important to your problem domain. 
*Create a repository that you can access at runtime for constant test values. This makes use of the General/OnceAndOnlyOnce principal, since you might have to tediously hand-code these sets - but you only do it once.

 - General/TimHart
*

I fully agree with you about "making the computer do the work", my problem is that until now, unit tests have only been about having me do more work (creating the tests) for unknown benefit. It is similar to adding documentation to my sources, I do like the thought about just running doxygen and getting full documentation for all my sources, but the extra work required by constantly having to ensure the documentation is uptodate (when code is restructured or created), and the question about who actually need this documentation (because I don't miss it myself), makes me postpone it... 

*
 Early on in a project - or any time you decide to add unit tests to a project - the "velocity" of the project will feel slow. As the practice of writing and running these tests is adhered to ( sometimes in conjunction with General/ContinuousIntegration ), the velocity of the project increases. As the number of lines in the project increases and the percentage of test coverage increases, so does the understood value and usefulness of these tests. - General/TimHart 

P.S. - this isn't a documentation thread, so I'll just say - Don't begin documentation until:

*Someone needs them
*The item being documented has stabalized somewhat

-- end soap box
*

**Many folks on the General/PortlandPatternRepository would disagree with you there, saying that documentation should be an intrinsic part of the development process like they would have research and testing be. Your mileage may vary.**

Yeah - I usually get into good scraps over that one. They usually end up by us drinking a beer and laughing about it. ;) In the kind of projects I'm involved in, if documentation is started early, it's a waste. No one ever sees it except the documenter until the end of the project. Nearly a third of the documentors time is used keeping the document up to date with the code, instead of doing something productive.

To balance my statement above - I do sincerely believe that once someone needs documentation ( or that date had been identified ), then work should be scheduled so that the document will be complete by that date. Some of the people I get into arguments with are involved in different types of work with different means of communication and requirements. In their world, documentation is essential. I usually duck that one by coming back with *Then you fall under my first bullet point above - you've identified that someone needs them*. -- General/TimHart
