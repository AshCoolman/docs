---
layout: post
title:  "Learn once, write domain once, write presentation anywhere"
description:  "My experiences with ReactNative have been very, VERY good"
date:   2017-04-17 13:36:22
categories: react, react-native, ios
---

#### Intro

One of the React mantas is:

> Learn once, write anywhere

Although not as catchy, my experience creating a prototype in react-native that maximally reuses web code (and QA testing) has been more like:

> Learn once, write domain once, write presentation anywhere

At the Saas company I work for, our modest 3 person FE team is extremely concerned with writing code; every line is a liability; but every line also pushes our product forward. The forces at play are multiplied when the app becomes cross-platform.

To us, a technology like react-native is a seductive proposition. There have been some established teams posting very high code reuse with react-native. But is 90% lines of code reuse number feasible for a our smaller team? Is it important? Is it otherwise suitable?

I am in the process of looking into this - and these are my findings so far.

##### Background

We have a largely greenfield web project:

* 2 months
* ~3-4 devs
* 12k LOC
* 78% test coverage
* Tech: Expo (formally exponent), create-react-app, react-router, some redux, styled-components, and react-bootstrap (phasing-out).

##### Goals

Everything we do is shaped around current and future development velocity. If we build basic functionality faster, it means more time for advanced functionality. If we can reduce ongoing QA, that means more intense bug testing, and automated regression tests in the future.

We have not really specced out a mobile app - but if we can get at least iOS for cheap, we would consider it. My prototyping efforts to help work out the approximate cost.


##### Fundamentals

Cross-platform apps share domain code, but not platform code. If you want to show a login button, semantically it is a login button for all platforms - but its actual rendering and underlying integration is platform-specific.

React is ostensibly the javascript library most obsessed with this seperation of function and form - revealed in its core-concept of a "virtual-DOM". You build React components that produce virtualDOM (domain), and map this to platform elements (platofrm). 

Libraries to map VirtualDOM to platform elements:

* [react-dom](https://www.npmjs.com/package/react-dom) web browser elements (div, span etc)
* [react-native](http://facebook.github.io/react-native) native elements (TouchableHighlight, ScrollView etc)

Note: react-native actually registers the components with the native system that bundles and runs app. Expo further helps the ios vs android mapping.


##### The cross-platform component

So to produce a cross-platform React component, the following _conceptual_ logic must happen somewhere:

```
if platform === 'web' then
  render('web/div')
else if platform === 'native-app' then
  render('native/View');
endif
```

Where is a good place to choose the platform?

Lets recap - React components are essentially functions with state, that build virtual DOM (domain), which maps to elements (platform).

Said another way, you want to reuse a function, but you want to _parametise_ it with different elements per platform.

This sounds like a natural fit for _Factory_ functions.

And convieniantly, React components fall neatly around this abstraction.


##### A simple example: React component factory

Here is a simple React component:

```
import React from 'react';

// This component with web elements
const Color = (props) => (
    <div>
        <span style={{width: '10px', height: '10px', backgroundColor: props.color}} />
        <span>{props.color}</span>
    </div>
);
Color.defaultProps = { color: 'black' }
export default Color;
```


Now lets turn this into a platform agnostic Factory function:

```
// Notice no platform specifc code here
const ColorFactor({Wrapper, Square, Label}) => {
    // Below is the component, 
    const Color = (props) => (
        <Wrapper>
            <Square color={props.color}/>
            <Label color={props.color} />
        </Wrapper>
    );
    Color.defaultProps = { color: 'black'}
    return Color;
)
```

And now to create the web version:

```
import React from 'react';
import ColorFactory from './ColorFactory';

const Square = ({color}) => <span style={{width: '10px',height: '10px', backgroundColor: color}} />
const Label = ({color}) => <span>{color}</span>;

export default ColorFactory({ Wrapper: div, Square, Label });
```

Now lets create the react native version:

```
import React from 'react';
import ColorFactory from './ColorFactory';
import { View, Text } from 'react-native';

const Square = ({color}) => <View style={{width: 10,height: 10, backgroundColor: color}} />
const Label = ({color}) => <Text>{color}</Text>;

export default ColorFactory({ Wrapper: View, Square, Label });
```


##### Reuse opportunities

You cannot reuse logic to do with platform elements, or platform APIs (must be wrapped). They must be all parameters to factory e.g.

* scroll events
* cookies (react-native has no synchronous persistance solution)
* User interactions (onPress vs onClick)

What I have not bothered to reuse:

* Nav - based on the possible wrong assumption that Nav between web and nav will be different, and have different tweak churn rates. 

The reuse I've gotten for cheap:

* domain logic
* lifecycle hooks
* network calls
* state management
* render callbacks & logic
* tests of the above (with some provisions)

Basically all the riskiest parts.

Another big benefit:

* Requirements alignment: the same dev creates both web and native
* QA testing of domain logic is reused (IN THEORY, TO BE TESTED)

##### Other benefits

###### Decoupling

You decouple your domain from platform elements. Component markup becomes the shared presentational code between platforms, increasing the importance of clarity. For instance, while preparing a web component for native I cam across a product card:

```
const Product = props =>
  <Wrapper>
    <img src={props.imgUri} /> <!-- <= Full bleed image !>
    <Card>
      <CardContent>
        ...
      </CardContent>
    </Card>
  </Wrapper>
```

The image appears "full bleed" at the top of card, while the rest of the Card content has padding. To get this effect with css you can:

1. organise the markup as above
2. put the image inside the card and give you a `calc()`'d' css rule to account for the padding

In the web word, 2. might be of dubious benefit - but in a cross platform world, the markup comes first so 2. is clearly the correct step - so you end up with:

```
<Card>
  <CardImageTop fullbleed source={props.source} />
  <CardContent>
    ...
  </CardContent>
</Card>
```

Thus no superflous wrapper, and semantic relationahip between card and its children in maintained. In a project with an eye to the future - this markup is far more [web-componently](https://developer.mozilla.org/en-US/docs/Web/Web_Components) and thus future ~proof~ aligned.


Plus, decoupling is generally a postitive trait for code to have. This harmonises well with TDD (also a positive force on decoupling).


###### Parametised children

You can swap out children using Factories. This can be for any reason, :

**Platform changes**: because you want to make something more harmonious for the platform (A "Parcel returns form" on web might have a text input, while app might have a bar code reader. 

**Incremental improvements**: Or perhaps you just want to rewrite a poorly written component and QA it on app before, back-flowing it to the web.

**A/B split testing**: perhaps you want to serve all customers with ids that end in 9 a special version of the component:

```
import React from 'react';
import ColorFactory from './ColorFactory';
import getClient from './getClient';
const Square = ...

// Split tested component
const Label = ({color}) => 
    getClient().test === 'A'
    ? <span>{color}</span> /* A */
    : <span>{color + "!"}</span>; /* B */

export default ColorFactory({ Wrapper: div, Square, Label });
```

##### Project organisation

Because we started web first, I just created a react-native project and installed the web project as an npm dependency. Its worth noting that `npm link` does not work with the react-native packager. A fix is incoming, so I currently use the npm registry (with and dist tags), or a bash script (to update my  `~/app-native/node_modules/app-web/src` dir from `~/app-web` folder). 

##### Some notes on tech

###### Expo (& create-react-native-app)

A bit time saver.

I'd previously played with react-native twice, both with custom written webpack configs. I'd constantly encounter problems where I would fall into a hole looking for a solution for hours e.g. linking certain native modules, importing native libraries, and having watchman die on me. I believe the last time I setup a project it took ~2.5 days to get ready to work on.

But with exponent setup took a few hours, plus I effortlessly was able to add in some things I'd not done before (custom fonts etc). And I only "fell in a hole" about 3 times in 2 weeks.

###### styled-components

These are _great_ for semantics, and there is a web and react-native varients, get involved.

###### react-base

Of the maintained UI frameworks for react-native this has the best, most expressive markup at the time of this writing. It also has great docs, and a reasonably healthy repo. The fact you have to buy into their slightly intrusive theming method makes me wonder if my team will want to transition away from it, but its a great starting point.

#### Conclusiom & Metrics of effeciency

So far in rebuilding 2 feature containers (~23 stateless components)
* In raw lines of code, adding a native components adds ~10-40% more code (need more info)
* Of the time taken to produce a web feature, it takes a further +5-50% extra to build in react-native

ReactNative has been _fun_ to work on. Its quick, its reasonably stable, its satisfiying and feels very effective. You begin to realise how _hostile_ the web environment is with all its loose standards. React-native gives you a nice goal to conform to

#### Ongoing Questions

* Need to get more clarity on the metrics of dev effeciency 
* QA time savings are still unknown
* I might look into running the web tests with react-native - but perhaps this is mathematically no-op? Another option is checking out how painful selenium tests are to write.