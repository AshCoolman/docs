---
layout: post
title:  "Cold CoffeeScript"
description:  "Why I no longer use Coffeescript, even if I love it"
date:   2016-06-15 16:40:12
categories: coffeescript
---

#### Intro

I do not have strong feelings on Coffeescript: I like Coffeescript, but I don't use it. Perhaps this means it is a thin topic for me to write about. But regardless, here is my probably parting, hopefully nonpolarising thoughts on the language I left behind.

#### Goodbye Vanilla, hello Coffee

In 2014 I fell in love with CoffeeScript. So much so it was soon the lingua-franca of my internal coding monologue. Anytime I returned to Javascript I felt weighed down by the unnecessary parentheses and curly brackets. I'd never used Ruby or Python, and at the time, I wasn't particularly into pure functional programming, I just found CoffeeScript _really nice_.

But in late 2015, after writing ~10,000 lines of CoffeeScript, I started to feel the pull of other transpiled languages. As it stands today, *I'm not sure I'll ever write in CoffeeScript again*. I'm often happy with the Javascript language features available in any evergreen-browser. And always happy to setup Babel in all other cases.

#### First hint of bitterness

In  2014, after using Coffeescript for 9-10 months I had sat down to build a little prototype in Coffeescript. I had decided to look into ES6 modules - but was quickly disappointed to find the [official CoffeeScript advice](https://github.com/jashkenas/coffeescript/wiki/FAQ#unsupported-features) was to _roll your own, #ES5style_. Coffeescript was actually _limiting_ me.

#### Weak Coffee?

So why wouldn't the Coffeescript guys adopt the TC39/community _blessed_ module system?

To quote from the [Coffeescript FAQ](https://github.com/jashkenas/coffeescript/wiki/FAQ#unsupported-features):

>Q: Will you add feature X where feature X depends on a platform?
>
>A: No, implementation-specific features are not allowed as a policy. Everything that you write in CoffeeScript should be supported and runnable on any current JavaScript implementation (in practice, this means the lowest common denominator is IE6). Thus, features such as the following will not be implemented: getters & setters

Which seems to mean Coffeescript:

* is happy to be left behind Javascript
* believes roll-your-own idiomatic ES5 is good enough
* has IE6 support in its DNA

But this "Coffeescript opinion" is not why I like Coffeescript. I like the obvious wins: multiline strings, terse syntax, english-like symbols. While I was _comfortable_ with CoffeeScript's opinions (no named functions, implicit returns etc), I do not see them as clear advantages. These are really constraints which subjectively can be helpful or hurtful.

And as for Coffeescript's IE6 support, this was never an important factor.

#### Humble path

Many of the advantages that Coffeescript had over the Javascript in 2014, no longer exist in a modern browser. And compared to other transpiled languages, Coffeescript is lagging behind. Not that the CoffeeScript maintainers seem to mind - after all they made an [extra considered](https://github.com/jashkenas/coffeescript/commit/6b4e437c93715a08b9f05b8423a953dda3a10a93), pleasant python-like language, and they want to keep it that way. Fair enough.

#### Dirty Chai Latte with soy, please

Perhaps I'm a filthy hipster, but Coffeescript's rate of adoption for new language features is too slow for me. I want everything:

- *async/await*:
    I _love_ this feature, but Coffeescript's [discussions are ongoing](https://github.com/jashkenas/coffeescript/pull/3813):
- *decorators*:
    I find these super convenient and so do [others](https://angular-2-training-book.rangle.io/handout/features/decorators.html). The main transpilers all have some level of support for (the admittedly [volitile](https://github.com/wycats/javascript-decorators)) proposal:
        - Babel 6.x [_added, then removed, added legacy supported_](https://phabricator.babeljs.io/T2645) for pending tweaks to the proposal
        - Typescript support seems pretty stable
        - CoffeeScript won't [get this Python inspired feature](https://github.com/jashkenas/coffeescript/issues?utf8=%E2%9C%93&q=is:issue+decorator+in:title+) at all. Instead recommending developers use a [es5-style decorator pattern](https://coffeescript-cookbook.github.io/chapters/design_patterns/decorator), and/or [rolling your own](https://github.com/rstuven/es-decorate).
- *generators*
    Coffeescript support for generators lagged behind Chrome by 4-5 months

#### Safe subset

Some new Javascript features don't make sense for Coffeescript. Others don't suit it ([getters/setters](https://github.com/jashkenas/coffeescript/issues/4156#issuecomment-161362692)). Coffeescript tries to leave the developer with the option to hack if they _really need to_. But otherwise, it gives the developer a restrained, and safe subset of Javascript.

Accept all this, and you can easily find many places where Coffeescript shines. I really love it in test suites (`describes` and `it` never looked so slick), and when working with libraries like Highcharts that use large of object literals. 

#### Unpopular

Unless you are coding in a cave somewhere, you need to respect your fellow developer opinions. And the prevailing opinion is people don't seem to like Coffeescript (even some of [those who love](https://github.com/michaelficarra/CoffeeScriptRedux) it). According to the 2016 StackOverflow survey it is [dreaded](http://stackoverflow.com/research/developer-survey-2016#technology-most-loved-dreaded-and-wanted) and it is [shrinking](http://stackoverflow.com/research/developer-survey-2016#technology-trending-tech-on-stack-overflow). 

_*sigh*_

I really wish everyone had experienced the joy of writing es5 code in a terse syntax - it really is nice

#### Final nail in the caffeine

The community, and thus useful libraries have embraced new languages features. I find myself enjoying the power and expressiveness of these new features. If I need limitations, I will build them in myself. Thus, my primary language is going to treat post-ecma 5.1 features with a sense of urgency.