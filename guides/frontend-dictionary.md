---
layout: page
type: guide
title: Frontend dictionary
permalink: /guides/frontend-dictionary/
---

# Practical frontend dictionary

_Updated: 2 Dec '15_

Practical info for a developer about to crack open a new frontend technology

## Similar work

* [State of the art javascript in 2016](https://medium.com/javascript-and-opinions/state-of-the-art-javascript-in-2016-ab67fc68eb0b)
* [Simplified JavaScript Jargon](https://github.com/HugoGiraudel/SJSJ)
* [frontendhandbook.com](http://www.frontendhandbook.com/)

## Bash

**One liner:**

Unix shell & command language that ships with OSX

**Should you use it:**

Probably;

It is handy. But its hard to understand if you don't understand the commands and flags being used.

Generally. if there is already a nice package (npm, homebrew), switch to that.

**Resources:**

* [Bash script mode](http://redsymbol.net/articles/unofficial-bash-strict-mode/)

## Bower

**One liner:**

A package manager for front-end, inspired by npm

**Should you use it:**

*No.*

Bower's strategy of maintaining a pre-built flies and a seperate manifest (bower.json), ontop of npm's package.json has not  worked. You can use npm's `postinstall` to acheive the same result. Normally I transform the sources directly using something like Webpack or Gulp. We may get a community "blessed" solution using something like [npmcdn](https://npmcdn.com/) sometime in the future. And even further in the future, ES6 module syntax plus multiplexing will start to reduce the bundling/transformation requirements even further.

## React

**One liner:**

An idiomatic Javascript view-layer library with an emphasis on functional programming and efficient manipulation of the DOM.

**Resources:**

- [Airbnb style guide](https://github.com/airbnb/javascript/tree/master/react)
- [Advantages of using inline CSS](https://speakerdeck.com/vjeux/react-css-in-js)

## React Native

_Updated: 5 Jan '17_

**One liner:**

A platform that allows native apps to be composed (and controlled) by Javascript.

- [ericvicenti/navigation/Docs/NavigationOverview.md](https://github.com/ericvicenti/navigation-rfc/blob/master/Docs/NavigationOverview.md)

## React navigation options

_Updated: 5 Jan '17_

There is many projects, some only for React, some for ReactNative and some for any.

### Navigator (ReactNative only)

**One liner:**

Old, original imperative navigation library - easy-to-use but rigid

### Navigator experimental (Any)

**One liner:**

New set of components built by ReactNative team with js primitives, that is harder to use than Navigator (no routing), but supports single-directional data flow in prop-driven "navigation views"

### react-router-native (ReactNative only)

**One liner:**

Built upon _Navigator experimental_ and mimics _react-router_ for the web

### react-native-navigation (ReactNative only) [link](https://github.com/wix/react-native-navigation)

**One liner:**

Built on native primitives, rather than js-primitives

## Webpack

_Updated: 27th Nov 2015_

**One liner:**

A build system, built by the React team.

**Nutshell:**

You point Webpack at single manafest file aka _entry point_. In this entry point list the files you want to include using javascript module syntax (e.g. `require()`, `import *` etc). All these files will be cleverly bundled up into a single file ready for production.

You can transform the files before they go into the bundle using loaders (e.g. turn `readme.md` into html). Webpack didn't let web specs hold it back, which means it has an opinionated method, but is very pleasant to use. There are many optional add-ons to make development easier: Hot loader, webpack-dev-server etc.

## AngularJS

**One liner:** _Updated: 2 Dec '15_

A framework provides useful, but older functionality when building single page apps.

**Nutshell:**

Released originally in 2009, Angularjs provided functionality to streamline building single page applications, at a time when organising large-SPAs were becomming common, cross-browser was a bigger time sink, and the Javascript/CSS/HTML specs were only starting to roll out the next generation of features.

In other words there was lots of rough parts of the internet, and the Angular chose the JQuery way, which was to smooth over the rough bits with code (e.g. ajax requests, data-binding), and build the missing parts (e.g. data binding, routing). Some of its functionality has been replaced over the years by new ideas, or new web specs.

**Should you use it:**

Probably not in most cases, as it is an older framework that solved older problems in an older way.

However, if you are considering it, compared to other frameworks it is:

- Unopinionated (unlike Aurelia or Ember): which means its great for small SPAs but the foot-shooting factor is high
- Augomagic with big API (unlike React which is more idiomatic): easy to get started in, but very hard to master (and hence flow). It has been suggested that Angularjs' high Google trends rating verses React might be because developers constantly need to go back to the Angular docs

## [Angular2](https://github.com/angular/angular/tree/master/modules/angular2)

_Updated: 2 Dec '15_

**One liner:**

The successor to AngularJS.

**Nutshell:**

It is basically a reaction to new language features, and making a framework that will neatly fit into the future web. Some examples of the large differences include replacing bespoke-module code Javascript module syntax, improving speed by using observables rather than dirty-checking, and introducing a web-component friendly templating syntax.

It was also an oppourtunity to reduce the API, and as such the many explicit module types from AngularJS have been replaced with a single everything-is-a-controller component that is _decorated_ with extra functionality_. Services, models and whatever else are regular Javascript objects (classes, functions, object literals etc).

**Should you use it:**

Definately if have an AngularJS brownfield - it is much better.

Unsure if you have a greenfield Angular 2 - it has lots of Features, but as a result lots of API/DSL

## Babel

**One liner:** _Updated: 24 Dec '15_

A highly configurable, post-es5 javascript transpiler with support for jsx markup, and _inference based_ type-system using inference (via Flow)

## Typescript

**One liner:** _Updated: 24 Dec '15_

Post-es5 javascript transpiler, with an _explicit_ type-system that is inbuilt

**Nutshell:**

TypeScript is a super-set of the JavaScript language that introduces types (plus interfaces and new JavaScript features). It allows type-checking during development time with supported editors such as Visual Studio. The compiler requires information about the “shape” of a module in a type-definition-file.

Note: Some contributions were first made to the [Simplified JavaScript Jargon](https://github.com/HugoGiraudel/SJSJ), and then copied here.

**Should you use it:**

Unsure: the tooling is shakey, and it is a large commitment.

**When starting:**

* You don't need `*.tds` files for `*.ts` files, but without them you don't get type-checking
* You **do** need a `*.tds` file for `*.js` files you import from `*.ts`

Some contributions first made to the [Simplified JavaScript Jargon](https://github.com/HugoGiraudel/SJSJ)

**Resources:**

* [Typescript deep dive gitbook](https://basarat.gitbooks.io/typescript/content/index.html

## Redux

**One liner:** _Updated: 22 Dec '15_

Client-side state managemer: not as powerful as Flux, but much easier

**In a nutshell:** _Updated: 23 Jan '16_

> It isn’t a real library like React or Relay. It’s a glorified global state event emitter! - [@dan_abramov](https://twitter.com/dan_abramov/status/678419254937526272)

Redux is a small library that represents state as (immunatable) objects. And _new states_ by passing the current state through pure functions to create an entirely new object/application states.

Reducers, actions, subscribe and dispatch are the core, and you could conceivable just use that. But practically speaking its worth learning the conventions + utility functions to reduce boilerplate / increase expressiveness. There are connector libraries for Angular2 and React.

**Deep dive:**

Redux does not represent changes in your application's state by modifying objects ( as you would with object-oriented paradigms). Instead changes are represented as the difference between the input object and the output object (`var output = reducer(input)`). If you mutate either `input` or `output` you invalidate the state.

To sum up another way, Redux represents state as "frozen object snapshots". With these discrete _snapshots_ you can save your state at a point, reverse state, and generally have more "accounting" for all state changes.

State of your app is _only_ changed by a category of pure functions called reducers. Reducers have 2 important properties:

1. They _never mutate_, returning newly built objects: This allows reasoning about input + output _without side-effects_
2. Their signature is _always_ `function name(state, action) {}`, so it makes it easy to compose them:

Assume the state looks like this and we are using `react-redux`:
        
        var theState = {
          _2ndLevel: {
            count: 0
          }
        }

We want to increment the count, so we make these reducers

    const INCR_2ND_LEVEL_COUNT = 'incr2NdLevelCount';

    function _2ndlevel (state, action) {
        switch (action.type) {
            case INCR_2ND_LEVEL_COUNT:
                var newState = Objectd.assign({}, state);
                newState.count++
                return newState;
            }
        }

    function topLevel (state, action) {
        switch (action.type) {
            case INCR_2ND_LEVEL_COUNT:
                return Objectd.assign({}, {_2ndLevel: _2ndlevel(state._2ndlevel)});
        }
    }

Note the use of `Objectd.assign({}, ...)` to create an entirely new objects in _each_ reducer:

Assuming we have wired up Redux to these reducers, then if we use Redux's event system to trigger a state change ...

        dispatch({type: INCR_2ND_LEVEL_COUNT})

...Redux will call:

        theNewState = topLevel(theState, action);

NOTE: `action` is from `dispatch()`

Now `theNewState` is an *entirely new object*.

Note: You can enforce immutability with [a library](https://facebook.github.io/immutable-js/) (or [new language features](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)), or just be careful to not mutate anything :D

For a deeper look, checkout [this video](https://www.youtube.com/watch?v=xsSnOQynTHs) by Dan Abramov (the creator).

**Should you use it:**

I've not seen a better solution for Angularjs, and Angular2. For React I've only used component state (Redux is miles better), but I'm not sure how it compares to all the other state management libraries (Flux, Reflux etc)

**Resources**:

* [SO : Why use redux over facebook flux](http://stackoverflow.com/questions/32461229/why-use-redux-over-facebook-flux)
* [Handcrafting an isomorphic redux application with love](https://medium.com/front-end-developers/handcrafting-an-isomorphic-redux-application-with-love-40ada4468af4)

## Elm 

_Updated 11th Mar 2016_

**One liner:**

Functional language designed for the web



**In a nutshell**

- _pure functional-language_:
        Because it is [_pure_](https://www.google.co.uk/search?q=pure+function), it means the only "[side-effect](http://eloquentjavascript.net/03_functions.html#h_EdyBGBF6y/)" is that pure flows, like `(Action) -> Model -> Update -> View`, are much neater than can be achieved with Javascript.
- _statically-typed_:
        The static type checker is something like Typescript's but because is more specialised/simple (only functional), it use is much less of a cognitive burden.
- _terse syntax_:
        The syntax at first seems odd when you are looking through the Javascript lens. But once you start using it, you will find many syntactic convieniances and freebies that make it a joy to use. It is kind of like a non-mathsy version of Haskell.
- _Reactive_:
        It uses [signals](https://github.com/yang-wei/elmflux) (like [js observables](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)) which just means changes or recorded on a timeline (array). Which is convienice in functional programming, as that array can be transformed in various ways e.g. Did the user double click? = Are there two click events within 200ms of each other _within the Signal_.
- _compiles to javascript_:
        You can setup workflows with gulp/webpack etc to write part or all of your applications in Elm

**Should you use it:**

You should _learn_ it, as as of the now (April '16) it is where javascript is heading.

I've not used it in production, but see here for a [case study](http://tech.noredink.com/post/126978281075/walkthrough-introducing-elm-to-a-js-web-app) where 3% of business logic is elm.

**Resources:**

* [http://elm-lang.org/](http://elm-lang.org/)
* [Architecture tutorial](https://github.com/evancz/elm-architecture-tutorial)
* [Community curated list](https://github.com/isRuslan/awesome-elm)

## Indexed db

**One liner:** _Updated: 27 Dec '15_

A no-sql database included in all modern browsers.

## _Unfinished_

Unit tests:

* exercise the smallest bits of testable software to check they behave as expected
* Assess the implementation
* Individual units (methods)
* want to construct a test set that satisfy a coverage criteria, structural

Integration tests:

* verify the communication paths and interactions between components to detect interface defects.

Component tests:

* limit the scope of the exercised software to a portion of the system under test, manipulating the system through internal code interfaces and using test doubles to isolate the code under test from other components.

Contract tests:

* verify interactions at the boundary of an external service asserting that it meets the contract expected by a consuming service.
* End-To-End tests - verify that a system meets external requirements and achieves its goals, testing the entire system, from end to end.

Original sources:

* [1](http://martinfowler.com/articles/microservice-testing/)
* [gulp-The vision, history, and future of the project](https://medium.com/@contrahacks/gulp-3828e8126466#.wbsd7nsui)
* [Angular 2 deps](https://daveceddia.com/angular-2-dependencies-overview/)
* [Structured programming](http://condor.depaul.edu/sjost/it130/documents/structured.htm)
