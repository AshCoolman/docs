# Practical frontend dictionary

_Updated: 2 Dec '15_

Practical info for a developer about to crack open a new frontend technology

## Webpack

_Updated: 27th Nov 2015_

**One liner:**

A build system, built by the React team.

**Nutshell:**

You point Webpack at single manafest file aka _entry point_. In this entry point list the files you want to include using javascript module syntax (e.g. `require()`, `import *` etc). All these files will be cleverly bundled up into a single file ready for production.

You can transform the files before they go into the bundle using loaders (e.g. turn `readme.md` into html). Webpack didn't let web specs hold it back, which means it has an opinionated method, but is very pleasant to use. There are many optional add-ons to make development easier: Hot loader, webpack-dev-server etc.

## AngularJS

_To be filled out_

## [Angular2](https://github.com/angular/angular/tree/master/modules/angular2)

_Updated: 2 Dec '15_

**One liner:**

The successor to AngularJS.

**Nutshell:**

It is basically a reaction to new language features, and making a framework that will neatly fit into the future web. Some examples of the large differences include replacing bespoke-module code Javascript module syntax, improving speed by using observables rather than dirty-checking, and introducing a web-component friendly templating syntax. 

It was also an oppourtunity to reduce the API, and as such the many explicit module types from AngularJS have been replaced with a single everything-is-a-controller component that is _decorated_ with extra functionality_. Services, models and whatever else are regular Javascript objects (classes, functions, object literals etc).

## Typescript

_Updated: 2 Dec '15_

**One liner:**

Javascript with types

**Nutshell:**

TypeScript is a super-set of the JavaScript language that introduces types (plus interfaces and new JavaScript features). It allows type-checking during development time with supported editors such as Visual Studio. The compiler requires information about the “shape” of a module in a type-definition-file.

**When starting:**

* You don't need `*.tds` files for `*.ts` files, but without them you don't get type-checking
* You **do** need a `*.tds` file for `*.js` files you import from `*.ts`


Some contributions first made to the [Simplified JavaScript Jargon](https://github.com/HugoGiraudel/SJSJ)
