---
layout: page
type: guide
title: Angular styleguide
permalink: /guides/angular-styleguide/
---

## Principles

### Minimise API

Angular (esp. 1) has a large API, a lots of magic. Surely part of the reason Angular _google trends_ higher than, React, is because Angular devs need to keep referring to the Angular docs. Angular has enough magical [DSL](https://www.google.co.uk/#q=Domain+specific+language+DSL+pattern+angular), resist adding anything by plain idiomatic JavaScript to allow more dev flow.

### Atomise into modules and packages

This means Angular 2 as a preference (avoiding module-via-library and embracing module-via-syntax) 
e.g. Bad `angular.module('angular-only-module-system')`, Good `import * as myPackage from 'my-package'`



## Guidance

### Lanuages


1. *ES5*: Strong no
1. *Babel*: Seems like you have everything you need
1. *Typescript*: Seems like you have everything you need, more awkwardly, but with the power of typing, but with the cost of having to set Ambient types to get the compiler to shut up etc.
1. *Coffeescript*: I've not yet googled a nice [decorator solution](https://github.com/rstuven/es-decorate) but I think business logic (which should be in services completely apart form the angular code) should be fine.

## Components 

### General design

1. Minor components (most): Dumb and stateless (except through property & events) - think regular Web components like `<input>`, `<video>`

2. Major (as few as possible): Co-ordinate intra-component comms, provides state (reflected in Minor components), reacts to events and sets store

### Angular 1

Make them look like angular 2 /es6 modules

**Bad:**

```javascript
angular
  .run('comp', compRun)
  .config('comp', compConfig)
  .module('app')
  .directive('comp', CompDirFunc)
  .controller('comp', CompCtrlFunc)
  .service('comp', ComService)
  .constant('comp', {duringConfig:1})
  .value('comp', {})
  
function compRum() {};
function compConfig() {};
// ..etc you get the idea

```

**Good**: As little Angular 1 as possible

app.ts:

```javascript
import * {comp} from 'comp';

angular
  .component('comp', comp)
```

components/comp.ts:

```javascript
import * {compService} from 'comp';

class {
  public static $inject = ['$location'];
  
  constructor(
    public $location
  ) {
    compService.save($location.url());
  }
}
```

[More info](http://toddmotto.com/exploring-the-angular-1-5-component-method/)

### Angular 2: Make it look like es6 modules

Just do it

## Naming conventions

John papa style guide

## Folder structure

Resist having anything but a `components` folder.

But realistically you might have

```
.
├── app.ts
├── component
│   ├── application
│   │   └── (main application component)
│   ├── major
│   │   └── component-name/(minimal, smart, marshal minor's, intra-component communication)
│   ├── minor
│   │   └── component-name/(maximise, stupid: only communicate through events/property binding)
│   ├── routes
│   │   └── (components to do with routes)
│   └── store
│       └── (data store implementation )
├── config
│   └── (config phases)
├── entry.js
└── lib
    └── (wonderfully written services that can be used by any project)
```
