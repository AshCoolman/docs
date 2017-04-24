---
layout: post
title:  "Cross-platfrom development with React"
description:  "I've been doing some prototyping with ReactNative, taking an existing web codebase to native"
date:   2017-04-17 13:36:22
categories: react, react-native, ios, android
---

#### Intro

Every line of code that pushes a product forward is also liability and must be tested and maintained. Using [React Native](https://facebook.github.io/react-native/) to reuse code for different platforms is a seductive proposition, infact some large established teams have reported code reuse of 70%-90% . I've been looking at the realities of using React Native for my company, which has four frontend developers.

#### Background

My engineering team's priority is to build our web offering fast. But if we can get an iOS/Android app relatively cheap- we would seriously consider it. Currently our web app is under development:

* 2 months of coding (~12k LOC)
* ~80% test coverage
* limited mobile designs

The web app uses the following technologies:

* react-router
* redux
* styled-components
* react-bootstrap

#### Cross platform development in React

Theoretically we can share all domain code between all platforms. A button that triggers a login process is semantically the same on Web and on iOS. But how that button is rendered, and the APIs available are platform-specific.

React components build a "virtual-DOM", which is then mapped to platform elements. [Web](https://www.npmjs.com/package/react-dom) and *[native](http://facebook.github.io/react-native) are the obvious platform targets, but proof of the virtual-DOM decoupling can be seen in other mappings e.g. [terminal](https://github.com/Yomguithereal/react-blessed), [VR](https://facebook.github.io/react-vr/), and [3d](https://github.com/Izzimach/react-three). 

So we have established the following _conceptual logic_ must appear in cross platform code:

```
if platform === 'web' then
  render('<button onClick="doLogin">Login</button>')
else if platform === 'native-app' then
  render('<TouchableHighlight />');
endif
```

But practically speaking, where is the best place to put this?

<sup> * react-native registers the components with the native system, which actually bundles and runs the app. [Expo](https://expo.io/) takes care of the iOS vs Android mapping.</sup>

###### Option A: map 1 native to 1 web

Projects like [react-native-web](https://github.com/necolas/react-native-web) rebuild the [react-native elements & API](https://github.com/necolas/react-native-web/blob/master/src/index.js) for the web. _React-native-web_ itself is particularily powerful and mature, and really lets you write for native, and get web almost for free. But it has some limitations which must be considered carefully:

* limited semantic elements (e.g. no checkbox)
* limited styling (e.g. no fixed positioning)
* no clear development path to take web design to native
* not compatible with react-native ui frameworks

In the cases where these limitations are acceptable, there is nothing more elegant. But anytime we need more than 1:1 mapping provided by react-native-web, including the styling, we would need to add code in our view-logic to create specific markup. 

I think this could might be viable if we spent time designing for react-native. But perhaps the "limited palatte" offered by _react-native-for-web_ would be 

###### Option B: platform switching expressions in render logic

Using expressions to render different markup per platform is possible. But I think it is quite obvious that arbitrary if/elses in a render function would quickly get messy. For this reason, I am discounting this option out of hand.


###### Option C: Use domain components parametised by platform

React components are functions with state, that build virtualDOM (domain), which maps to elements (platform). We can use functions to create React components _parametised_ with platform elements. Thus we essentially need a [function to create a function](https://www.ibm.com/developerworks/library/j-ft10/) (depending on your programming-heritage, you may want to rename `LoginPartial` to `LoginFactory` in the example below).

```jsx
import api from 'api';

function LoginPartial({Wrapper, Header, Button}) 
  class Login extends React.Component {
    constructor() {
      super();
    }
    handleLogin = () => {
      api
        .post(this.context.loginDetails)
        .then(this.context.router.push('/home'))
        .catch(err=>this.setState( ()=> { err }))
    }
    render() {
      <Wrapper>
        <Header>Press to login</Header>
        <Button onClick={handleLogin}>{err || 'Login'}</Button>
      </Wrapper>
    }
  }
  return Login; // Factory returns newly parametised component
}
```

Web:

```jsx
const Login = LoginPartial({
  Wrapper: p=><div {...p} />,
  Header:p=><h1 {...p} />,
  Button: p=><button {...p} />)
})

ReactDOM.render(<Login />);
```

Native:

```jsx
import { View, Text, TouchableOpacity as Touch} from 'react-native';

const Login = LoginPartial(
  Wrapper: View,
  Header: Text,
  Button: p=> <Touch onPress={p.onClick}><Text {...p} /></Touch>
);
AppRegistry.register(<Login />);
```

Why I like this approach best:

* Component markup is left purely domain focused
* Complete flexibility of what is rendered
  - Necessary to decouple semantic tag from element tags
  - Side benefits:
    - Easy to split-test sub-components
    - Easy to incrementally upgrade sub-components

#### Overall impressions so far

I converted two large React container components (and ~23 stateless components) from the web project. I relied only on the technique described in option C, and tried to do an close-approximation of the web design converted to native.

Some quantative estimations:

* Of the time taken to produce a web feature, it takes a further +5-50% time to convert to react-native
* In raw lines of javascript, converting a web component to native adds ~10-40% more code

Some qualitive benefits:

* single developer per feature, means the cost to understand requirements is paid only once
* signifigant of QA reuse (unsure exactly how much)

The big con:

* React Native instability means development can be intially slow while workarounds & solutions are found

#### Ongoing Questions

* How much confidence does the existing web unit/component tests give? 
* What is the DX of selenium tests for native apps?
* How to best organise multi-platform codebases?

#### Technology notes

###### Expo (& create-react-native-app)

A big time saver. IIRC the last time I setup a react-native project using a custom webpack config it took ~1.5 days until I was happy to develop on it. Expo setup took a few hours, was more stable, and much easier to extend with custom features.

###### styled-components

These harmonise well with the domain-markup-only-components you end up with in Option C.

###### react-base

At the time of this writing, of all React Native UI frameworks, this has the best blend of expressive markup, realworkd components, docs, and community support. The theming method is quite a buy-in, and in general we will probably transition away in the medium term - but its a great starting point.
