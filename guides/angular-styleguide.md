## Principles

### Minimise API

Angular has enough API and [DSL](https://www.google.co.uk/#q=Domain+specific+language+DSL+pattern+angular) already. You should **resist adding anything except plain idiomatic JavaScript**. It has been suggested the reason Angular _google trends_ higher than React, is because Angular devs need to constantly google the docs.

## Guidance

### Which version of Angular?

Angular 2 should be used for all green field projects.

### Lanuages

1. *Typescript*: Angular 2 built with typescript so it is the obvious choice
1. *Babel*: Babel is good, though it's a bit of a wild ride (Decorators in and then out, painful transition from v5-v6)
1. *ES5*: Limiting yourself to ES5 will cause more boilerplate
1. *Coffeescript*: I've not yet googled a nice [decorator solution](https://github.com/rstuven/es-decorate) but I think business logic (which should be in services completely apart form the angular code) should be fine

## Components 

### General design

1. **Minor components (most in number)**: Dumb and stateless (except through property & events) - think regular Web components like `<input>`, `<video>`

2. **Major (least in number)**: Co-ordinate intra-component comms, provides state (reflected in Minor components), reacts to events and sets store

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

## Style guide

[John papa style guide](https://github.com/johnpapa/angular-styleguide) is a good place to start

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
