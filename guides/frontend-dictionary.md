# Practical frontend dictionary

_Updated: 2 Dec '15_

Practical info for a developer about to crack open a new frontend technology
## React

**One liner:**

An idiomatic Javascript view-layer library with an emphasis on functional programming and efficient manipulation of the DOM.

**Resources:**

- [Airbnb style guide](https://github.com/airbnb/javascript/tree/master/react)

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

**Should you use it:**

Unsure: the tooling is shakey, and it is a large commitment.

**When starting:**

* You don't need `*.tds` files for `*.ts` files, but without them you don't get type-checking
* You **do** need a `*.tds` file for `*.js` files you import from `*.ts`


Some contributions first made to the [Simplified JavaScript Jargon](https://github.com/HugoGiraudel/SJSJ)

## Redux

**One liner:** _Updated: 22 Dec '15_

A small library, that represents changes in state by using pure functions to compose entirely new application states.

**In a nutshell:** _Updated: 31 Dec '15_

The first major aspect is a `dispatch({type: SOME_ACTION})` and `subscribe(myStateChangeHandler)` system. State of your app is _only_ changed by a special class of functions called reducers. Reducers have 2 important properties:

1. They never _mutate_, returning newly built objects: This allows reasoning about in + out _without side-effects_
2. Their signature is _always_ `function name(state, action) {}`, so it makes it easy to compose them:
```
  /*
    Assume the state looks like this:
    
    var theState = {
      _2ndLevel: {
        count: 0
      }
    }
    
    If we make this call...
    
      dispatch({type: INCR_2ND_LEVEL_COUNT})
    
    ...Redux will call:
    
      theState = topLevel(theState, action); // action is passed from the dispatch
    
  */
  
  function _2ndlevel (state, action) {
    switch (action.type) {
      case INCR_2ND_LEVEL_COUNT:
        var newState = Objectd.assign({}, state);
        newState.count++
        return newState;
    }
  }
  
  function topLevel (state, action) {
    switch (action.type):
      case INCR_2ND_LEVEL_COUNT:
        state._2ndLevel = _2ndlevel(state._2ndlevel);
        
  }
```

Reducers, actions, subscribe and dispatch are the core, and you could conceivable just use that. But practically speaking its worth learning the conventions + utility functions to reduce boilerplate / increase expressiveness. From my experience these learnings were interchangable between Angular2 and React.

**Should you use it:**

I've not seen a better solution for Angularjs, and Angular2. For React I've only used component state (Redux is miles better), but I'm not sure how it compares to all the other state management libraries (Flux, Reflux etc)

## Indexed db

**One liner:** _Updated: 27 Dec '15_

A no-sql database included in all modern browsers.

## _Unfinished_

Unit tests:

* exercise the smallest pieces of testable software in the application to determine whether they behave as expected.

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
