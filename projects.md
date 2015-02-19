---
layout: page
title: "Projects"
description: "A list of my projects"
---
### Android projects

1. [Concentrato][1] is the first Android app I ever wrote. It is a time management
app based on the Pomodoro technique. It needs a major refactor, because I knew
very little about architecture when I started it, and it's now a bit hard to work
on. It has a fairly major bug which I will be fixing asap, once I've refactored
the architecture a good bit. The source is up on [BitBucket][8].

2. My second app is still in development. It's currently codenamed [Yacc][2] (Yet
Another Currency Converter) but it's release name will be something different,
particularly as there's an existing [parser generator][3] by that name. It's
getting quite near release, but I've been pretty perfectionist on this one. I've
spent a lot of time thinking about how best to architect things, reading a lot,
and generally taking my time over it. I've found it a very useful learning
exercise doing things this way, even if it has delayed it's release a lot.

3. I want to do a puzzle game, but haven't really got started on that yet, just
some ideas about game mechanics. This will again be a learning exercise (aren't
all projects learning exercises?), but hopefully I can do something that's at
least fun to play.

### Haskell projects

I don't have many signifcant-sized Haskell projects yet, mainly just lots of
little things done in the course of learning. Nonetheless, here's some links to
some repos:

1. A [small project][9] to take a list of tasks from <RememberTheMilk.com>, render
them to Markdown and stick them in a Wordpress draft post. This is to help me
carry out weekly reviews of what I've done/how I'm doing with goals and projects.
It involves using the [Wordpress REST and OAuth2.0 API][10]. I'd quite like to
expand it into a library for accessing the Wordpress API, but I haven't found any
good resources for designing a Haskell library for accessing a REST API.

2. I've got a very early stage project to algorithmically generate fairly simple
vector graphics (actually because I can never find T-shirt designs I like!) using
the [Diagrams library][11], but this is still in a private repo for now. I'll open
it once I've done a bit more on it.

2. I've solved lots of the [99 Haskell problems][4] which can be found in [this
Mercurial repo][5].

3. I've solved some of the [Project Euler][6] problems [here][7].



[1]: https://play.google.com/store/apps/details?id=com.omricat.concentrato
[2]: https://github.com/grodin/yacc
[3]: http://en.wikipedia.org/wiki/Yacc
[4]: https://wiki.haskell.org/H-99:_Ninety-Nine_Haskell_Problems
[5]: https://bitbucket.org/grodin/learnyouahaskell-99haskell-problems
[6]: https://projecteuler.net/problems
[7]: https://bitbucket.org/grodin/project-euler
[8]: https://bitbucket.org/grodin/concentrato
[9]: https://bitbucket.org/grodin/rtm-completed-tasks-to-wordpress
[10]: https://developer.wordpress.com/docs/api/
[11]: http://projects.haskell.org/diagrams/