---
layout: post
title: "Resuscitating Rhythmweb"
date: 2014-05-23 22:50:00
excerpt: "working with legacy code"
tags: [tdd, refactoring, legacy code, python, opinions]
---

There was an old project, it started based on an ancient ppc ubuntu that was running on top of a mac mini G4, built for python 2.4 in an environment that did not accepted installing any dependency.

Back in the day I did not knew what a unit test was, so I built it the old way: code a little, try it, code a little more, make it work again, etc, etc.
You can tell if you see the old code, it is full with logging messages to check if the execution reach a certain portion of the code, oh yeah, no debugging was possible.

In the middle of the road, rhythmbox evolved, with [gnome-shell](http://www.gnome.org/gnome-3/) rhythmbox adapted to [GTK3](http://www.gtk.org/), breaking a lot of things. 
And finally it also changed from python2 to python3, breaking the rest of things.

There is a lot of work to do to bring it back. The way I use it is turn my mobile phone into a remote control for my music player: [rhythmbox](https://wiki.gnome.org/Apps/Rhythmbox),
 that way I can select what I want to hear without turning on the screen or reaching a keyboard, both things I'm not able to do any time. 
 I also like the idea of controlling everything from one device.


## The initial project status

[Abandonware](http://en.wikipedia.org/wiki/Abandonware) is probably the word that fits better. 
Shame on me, as I ignored the project for a long time, mainly because I did not needed it. 
But I do need it back, or maybe it is just that I have some time to dedicate to it, plus I feel embarrassed by that code:
never underestimate the power of public shame.

Let's face it, I did not wanted to change a thing, it was still kinda running on rhythmbox on gtk3, python2.7, 
which was what I had in my old debian based rhythmbox on the TV box: I did not needed to code anything to use it...

## The public shame

I missed it, and progress is progress, and also there is the shame. I needed to do something about it; so, I brought it into my 
[github](https://github.com/pcarranza/rhythmweb) account, setup the project on [travis-ci](https://travis-ci.org/pcarranza/rhythmweb) 
and the most important, add the [badge with the coverage percentage](https://coveralls.io/r/pcarranza/rhythmweb), 
so the shame becomes public.
Also I added a roadmap to the readme, which in this case works as a wish list, all the things I want to happen to the project.

## How to work with legacy code?

_Legacy code_ is any code that is not covered by a test. With that definition, you can write legacy code right away, you only need to code, 
and not code tests.

Initially this project had a coverage of a flawless __0__, so it was all legacy code.

There is a lot of refactor to do, but it is not possible to refactor without tests, 
that is like skydiving with a backpack, any backpack, maybe you get lucky and it is a parachute, probably not.

This is where I am right now, writing tests. 

This can be tricky, you can spend all your time chasing a 100% coverage number without adding any value to the code, I did that already and I know that nothing good comes out of it, so lets setup a ground rules:

* Coverage is a tool, not a goal: it's not about the 100% number, I have to agree with @dhh; coverage is not a magical metric that will make the code perfect, it is just a tool that will let you know what is being tested and what not, it's a matter of common sense to determine if it is enough or not to stop.
* No refactor until basic usage is covered: this is a basic, let's not introduce new bugs on a code that is bad enough.
* No mocking or patching beyond isolating rhythmbox logic or WSGI logic: again, heavy mocking or monkey patching would hide bugs inside the mocks, so let's try to use actual objects when possible and keep mocks under control.
* All changes will be made while using the tool (eat your own dogfood)
* Use travis and coveralls: for gamification
* Declare what is the direction of the project up from in the roadmap to keep focus

With all that, I'll resuscitate this project. Stay online for updates.
