---
layout: post
title:  "Cold coffee"
description:  "I love Coffeescript, but I've found myself no longer using it. Unlike much of the internet dicussion around Coffeescript, I'm not really too fussed either way. But I do have opinions, and a personal experience, so here is my (parting?) thoughts on the language."
date:   2016-06-15 16:40:12
categories: coffeescript
---

## Intro

I love Coffeescript, but I've found myself no longer using it. Unlike much of the internet dicussion around Coffeescript, I'm not really _too fussed either way_. But I do have opinions, and a personal experience, so here is my (parting?) thoughts on the language.

## 2014: Goodbye vanilla, hello Coffee

In 2014 I fell in love with a a codebase that used Coffeescript. I'm not a Ruby/Python or Haskell guy, so some things looked a bit strange. But soon, Coffeescript was what I thought in and it gave me wings. Anytime I went back to Javascript I felt weighed down by parenthesies and curlys.

But in late 2015, and 10,000 lines of Coffeescript later, I started to feel the pull of other transpiled langauges. As it stands today, *I'm not sure I'll ever write in Coffeescript again*. Infact, I'm pretty happy with the js engine in any evergreen browser out there. Given just how uncomfortable I used to be with Javascript, this is a huge reversal.

## First hint of bitterness

In late 2014, having a spare 40 minutes, sat down to build a little prototype in Coffeescript. I decided to use ES6 modules - but a few minutes later I was displeased to read the [official Coffeescript advice](https://github.com/jashkenas/coffeescript/wiki/FAQ#unsupported-features) was to _roll your own, #ES5style_. I remember mulling over my options for a long time. 40 minutes later I unhappily closed a file that looks something like...

```coffee
# my-function.es6-module.coffee
myFunction (param1)=>
    # My implementation
    # ...
    #
`export default myFunction; // Coffeescript doesn't support`
```


## Weak Coffee?

So why wouldn't the Coffeescript guys adopt the TC39/community _blessed_ module system?

To quote from the [Coffeescript FAQ](https://github.com/jashkenas/coffeescript/wiki/FAQ#unsupported-features):

>Q: Will you add feature X where feature X depends on a platform?
>
>A: No, implementation-specific features are not allowed as a policy. Everything that you write in CoffeeScript should be supported and runnable on any current JavaScript implementation (in practice, this means the lowest common denominator is IE6). Thus, features such as the following will not be implemented: getters & setters

Now, in my interpretation this means:

* An opinion that isn't lock-step with new Javascript proposal
* Trend toward roll-your-own idiomatic ES5 
* IE6 support in its DNA

Oh Coffeescript, you used to be so edgey! When I was first started with you I was all like:

> Hm, eliminating curlys makes my code a tad more readable...Man this function syntax rocks....Destructured assignment is awesome!...Hot dog! mutli-line strings - I'm in love!!!...

That stuff is bread-and-butter savings. Not once did I say to myself:

>Transpiling gets me IE6 support?...Gee whiz that floats my boat!

## Roadmap: The humble path

Many of the advantages that Coffeescript had over the Javascript in 2014, no longer exist in a modern browser. And compared to other transpiled languages, Coffeescript is lagging behind. Not that the Coffeescript maintainers seem to mind - after all they made an [extra considered](https://github.com/jashkenas/coffeescript/commit/6b4e437c93715a08b9f05b8423a953dda3a10a93), pleasant python-like language, and they want to keep it that way. Fair enough.

## Dirty Chai Latte please

Perhaps I'm a filthy hipster, but Coffeescript's rate of adoption for new language features is too slow for me:

- *async/await*:
    I _love_ this feature, but Coffeescript's [discussions are ongoing](https://github.com/jashkenas/coffeescript/pull/3813):
- *decorators*:
    I find these super useful and so do [others](). The main transpilers all have some level of support for (the admitedly [volitile](https://github.com/wycats/javascript-decorators)) proposal:
        - Babel 6.x [_added, then removed, added legacy supported_](https://phabricator.babeljs.io/T2645) for pending tweaks to the proposal
        - Typescript support seems pretty stable
        - Coffeescript won't [get this Python inspired feature](https://github.com/jashkenas/coffeescript/issues?utf8=%E2%9C%93&q=is%3Aissue+decorator+in%3Atitle+) at all. Instead reccommending developers use a [es5-style decorator pattern](https://coffeescript-cookbook.github.io/chapters/design_patterns/decorator), and/or [rolling your own](https://github.com/rstuven/es-decorate).
- *generators*
    Coffeescript support for generators lagged behind Chrome by 4-5 months

## Its own beast

Some new Javascript features don't make sense for Coffeescript. Others don't suit it ([getters/setters](https://github.com/jashkenas/coffeescript/issues/4156#issuecomment-161362692)). Coffeescript tries to leave the developer with the option to hack if they _really need to_. But otherwise, it gives the developer a restrained, and safe subset of Javascript.

Accept all this, and you can easily find many places where Coffeescript shines. I really love it in test suites (`describes` and `it` never looked so slick), and when working with libraries like highcharts that use large of object literals. So I _would_ occasionally use Coffeescript, except...

## The final nail in the caffeine

People don't seem to like Coffeescript (even some of [those who love](https://github.com/michaelficarra/CoffeeScriptRedux) it). According to the 2016 StackOverflow survey it is [dreaded](http://stackoverflow.com/research/developer-survey-2016#technology-most-loved-dreaded-and-wanted) and it is [shrinking](http://stackoverflow.com/research/developer-survey-2016#technology-trending-tech-on-stack-overflow).

However unless you are coding in a cave somewhere, you need to respect your fellow developer opinions. Though I wish everyone had experienced the joy of writing es5 code in a terse syntax - it really did clear away some of cognitive load when staring at code. Had more people used it, perhaps there would be more community support to rollin new javascript language features.

