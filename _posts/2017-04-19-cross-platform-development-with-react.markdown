---
layout: post
title:  "Cross-platfrom development with React"
description:  "I've been doing some prototyping with ReactNative, taking an existing web codebase to native"
date:   2017-04-17 13:36:22
categories: react, react-native, ios, android
---

#### Intro

Every line of code that pushes a product forward is also liability and must be tested and maintained. Using ReactNative to reuse code is a seductive proposition. Some large established teams have reported code reuse of 70-90% between web and mobile apps. I've been looking at the realities of using ReactNative for my company which has only 4 frontend developers.

#### Background

My engineering team's priority is to build our web offering fast. But if we can get an iOS/android app relatively cheap using React Native we would consider it. Currently we have:

* 2 months of coding on our web app (~12k LOC)
* 4 frontend devs
* ~80% test coverage

The react web project currently:

* react-router
* redux
* styled-components
* react-bootstrap

#### Cross platform development in React

Theoretically we can share all domain code between all platforms. A button that triggers a Login process is semantically the same on Web and on iOS. But how that button is rendered, and the APIs available are platform-specific.

React components build a "virtual-DOM", which is then mapped to platform elements. [Web](https://www.npmjs.com/package/react-dom) and *[native](http://facebook.github.io/react-native) are the obvious targets, but proof of the virtualDOM decoupling can be seen in other mappings e.g. [terminal](https://github.com/Yomguithereal/react-blessed), [VR](https://facebook.github.io/react-vr/), and [3d](https://github.com/Izzimach/react-three). 

Conceptually, there must be logic to map platform to element:

```
if platform === 'web' then
  render('<button onClick="doLogin">Login</button>')
else if platform === 'native-app' then
  render('<TouchableHighlight />');
endif
```

But where is the best place to put this?

<sup> * react-native actually registers the components with the native system that bundles and runs app. [Expo](https://expo.io/) further helps the ios vs android mapping.</sup>

##### Option A: map 1 native to 1 web

Projects like [react-native-web](https://github.com/necolas/react-native-web) rebuild the [react-native elements & API](https://github.com/necolas/react-native-web/blob/master/src/index.js) for the web. React-native-web itself is particularily powerful and mature, and really lets you write for native and get for web almost for free. But it has some limitations which must be considered carefully:

* Limited semantic elements (e.g. no checkbox)
* Limited styling (e.g. no fixed positioning)
* no clear development path to take web design to native
* not compatible with react-native ui frameworks

Anything outside the the 1:1 mapping of elements would need to be overriden with specific markup. In this case, this is outside of the library design and potentially breaks the semantic mapping between platforms.

In the cases where these limitations are acceptable, there is nothing more elegant.

##### Option B: platform switching expressions in render logic

Using expressions to render different markup per platform is possible. But I think it is quite obvious that arbitrary if/elses in the React component render function would quickly get messy. For this reason, I am discounting this option out of hand.


##### Option C: Use domain components parametised by platform

React components are functions with state, that build virtual DOM (domain), which maps to elements (platform). We can use functions to create React components _parametised_ with platform elements. We essentially need a [function to create a function](https://www.ibm.com/developerworks/library/j-ft10/) (component):

Note: Depending on your programming-heritage, you may want to rename `LoginPartial` to `LoginFactory`.

```
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

```
const Login = LoginPartial({
  Wrapper: p=><div {...p} />,
  Header:p=><h1 {...p} />,
  Button: p=><button {...p} />)
})

ReactDOM.render(<Login />);
```

Native:

```
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

* Of the time taken to produce a web feature, it takes a further +5-50% extra to build in react-native
* In raw lines of javascript, adding a native components adds ~10-40% more code

Some qualitive benefits:

* Singular dev per feature, means cost to understand requirements is paid only once
* Lots of QA reuse, unsure exactly how much

The big con:

* React Native development can be slow due to recurring problems, whose solutions can take many hours find

#### Ongoing Questions

* How much confidence does the existing web unit/component tests give? 
* What is the DX of selenium tests for native apps?
* How to best organise multi-platform codebases?

#### Technology notes

##### Expo (& create-react-native-app)

A big time saver. IIRC the last time I setup a react-native project using a custom webpack config it took ~1.5 days until I was happy to develop on it. Expo setup took a few hours, was more stable, and much easier to extend with custom features.

###### styled-components

These harmonise well with the domain-markup-only-components you end up with in Option C.

###### react-base

At the time of this writing, of all React Native UI frameworks, this has the best blend of expressive markup, realworkd components, docs, and community support. The theming method is quite a buy-in, and in general we will probably transition away in the medium term - but its a great starting point.
