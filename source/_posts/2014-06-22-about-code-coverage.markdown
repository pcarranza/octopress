---
layout: post
title: "About refactoring and code coverage"
date: 2014-06-22 01:19:00
excerpt: "blindness and assurance"
tags: [refactoring, legacy code, coverage, testing, opinion]
---

On the [previous post](http://pcarranza.github.io/2014/05/resuscitating-rhythmweb.html) I was talking about [legacy code](https://github.com/pcarranza/rhythmweb), considering legacy code as any code that has no tests. 

As an excercise I was going to recover this project first by adding some testing layer and then refactoring.

In this process I really learned a lot, first about the mindset change that using a completely different language such as Java or Python is: one thing is to write some code in python with a Java style (as it was before) and another completely different thing is understanding how to code in a given language, the idiom, style and patterns that apply to one language does not necessary apply to other.

Second: my code sucks, really; reviewing your own code a few years later is a great humble process, this gives you a lot of perspective at how much you sucked then, and make you wonder how much you will suck now to your future self.

And third: it is really hard to measure quality in any objective and reliable way, some people will talk about code coverage as the ultimate quality index, well, it is not; yes it is useful, but you cannot rely on coverage as the only quality measure. More important than that is common sense and making the right design decisions.

## Testing, mocking and code coverage

Mocking is actually lying, but in this particular case it is directly impossible to use the real objects that handle Rhythmbox because those are not accessible without a Rhythmbox instance; and it is not easy to automatically launch a Rhythmbox instance and then run tests from there; this can be considered as a project in itself. In fact that would be system testing, and in this case I'm going as far as unit testing with some manual system testing using curl.

On the good part, code coverage will give you the visibility to understand what you are actually testing, and what is in a reliable enough status, and with that you can refactor code, removing code that is actually impossible to test applying late [YAGNI](http://en.wikipedia.org/wiki/You_aren't_gonna_need_it) and cleaning up the code to make is subjectively better, which is the most I can expect.


The way I faced starting to cover the code was to create a conceptual business layer that did not existed in the code and then starting to cover only this layer while asserting that the mocks were called with the right arguments. I also used some stubs to avoid the 'mock reply to all' pattern as it hides bugs that you will only find when running the code for real.

This alowed me to focus on that conceptual business logic layer and start pushing complexity up and down leaving only what I considered to be application business logic. After that responsibilities started to be more clear, and with this iterative process, pieces started to fall into it's natural place one by one.

## The refactoring log

- With initial coverage, I refactored configuration and logging just for simplification, it helped later. 
- Some dead code was removed, including the [templating engine](https://github.com/pcarranza/rhythmweb/commit/8439ac0fb3b15b3810e9740697fdd959dd06afba) that was actually not in use, but that was not visible due to excesive complexity. And I was honestly afraid of removing anything without proper testing.
- When I reached [some good coverage](https://coveralls.io/builds/801583) of the rest layer I replaced the [json parsing and writing implementation](https://github.com/pcarranza/rhythmweb/commit/9c0e2f2406476df5632bfecff4e1aae4e156a4af) that was there from the G4 times with the current python json implementation, less is more.
- Around this point I realized that it was quite easy to [introduce bugs using mocks](https://github.com/pcarranza/rhythmweb/commit/62c2e1d022e727f6e77b2578ce15e0868d93a16f) so I added an [acceptance test that I can run from a terminal](https://github.com/pcarranza/rhythmweb/commit/2ab2e2bd19b840ae79149f9d35f18118ddb3bd13).
- The I started pushing complexity out of the rest layer into a controller layer: [song](https://github.com/pcarranza/rhythmweb/commit/c6b685b1c47a953a5d30d5d3f03ef317679e4f9e), [queue](https://github.com/pcarranza/rhythmweb/commit/06361f9a1f96270d595a48550c0eafb64ebfff51), [player](https://github.com/pcarranza/rhythmweb/commit/12858f5ce871e66c79bf628cc85e64880522276d) and finally [query](https://github.com/pcarranza/rhythmweb/commit/ec80ccc5d07b29998f4913fe710559481e6558e7).
- With that covered, I [removed the rhythmbox handler argument that was being passed around](https://github.com/pcarranza/rhythmweb/commit/6e06a554c620da357588788deb78dad7cfbf6788) and added that as a module value. I was growing on confidence, so I started to do major changes.
- So I started to go down, and started to add coverage for the rhythmbox handler object, with the goal of [removing the multiple inheritance](https://github.com/pcarranza/rhythmweb/commit/1b442a578841edb6a7f6a5355d116fc4ab5d1dda) and collapse all the code in one object so it can then be simplified and then splitted again.
- Then refactored too fast without having proper coverage to finally discover a [bug that exploded in my face](https://github.com/pcarranza/rhythmweb/commit/b8cfbcf5253ac5a8d18c179cd3ab6fa237aafe7c)
- Worked hard on getting real coverage on the rbhandler object, one responisiblity at a time, to finally get the query part refactored, and [looks way much better](https://github.com/pcarranza/rhythmweb/commit/9fc1116209b129542387b177f048e7ef99e08551).
- Finally, just to make sure that things keep running I added some full stack integration testing, basically it launches the server with all the stack and [gets a page](https://github.com/pcarranza/rhythmweb/commit/7cdc9e60e63ec7ad5df982ed60f3dcf034b8f1c5), some [page not found error and 500 server error tests](https://github.com/pcarranza/rhythmweb/commit/2929512aee8c1d65d0103c7dc64756c821a04323), and then [posting data test](https://github.com/pcarranza/rhythmweb/commit/b34b09bf9bcefb82421940316a157e8c8fc9eeec). 

While doing this I changed some things on the server implementation (simplification) and I'm in my way to change all the routing implementation, I can do it because I can test with a full stack integration test that it will keep working.

## The coverage percentage as quality measurement

The funny thing I found out is that while refactoring I tend to write less code, if this code was covered I will actually reduce the covered absolute number of lines, and also the total number of lines will be reduced. But the funny thing is that in the coveralls metric my refactored code (with better style) as a lower punctuation.

The case of the [rhythmbox handler simplification lost 1.21% of code coverage](https://coveralls.io/builds/870038), and it was just better to have it all on plain sight than spread across multiple objects that where inherithed into the same. But the simple fact that some class definition code was not being imported anymore this hitted the code coverage, producing this 'bad result' lowering the score.

And even worse, the [query refactoring](https://coveralls.io/builds/884174) that produced a code that has way better style ([SOLID wise](http://en.wikipedia.org/wiki/SOLID_\(object-oriented_design\))) also suffered of a 0.1% coverage drop because it simply was less code.

## Final thoughts

Is code coverage a bad thing? or unnecessary? not at all.

In this particular case, code coverage was a very usefull tool that allowed me to understand where to go next, starting with a thin layer of testing and then moving up and down from there, watching what was touched with each new test and refactoring these pieces of code to a better shape slowly.

In the land of the blind, the one-eye man is king; and code coverage is an eye into your code.

But don't take the percentage of code covered as an indicator of quality, it is just an indicator of visibility. 

A lot of the code that is still in the project sucks and must be refactored, I'm still working on it.
